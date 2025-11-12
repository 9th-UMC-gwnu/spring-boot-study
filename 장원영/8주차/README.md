Chapter 7. API & Swagger & Annotation
===
목차
---
* 계층/트랙젝션/예외 흐름
* API 응답 통일과의 접점
* DTO/Entity/Converter 예시
* Service/Repository, 트랙젝션 경계
* Swagger(springdoc) 세팅
  * 의존성, 기본 OpenAPI 설정
* 어노테이션 기반 Validation
---

### 계층/트랙젝션/예외 흐름
- Controller: 요청 바인딩과 검증 트리거(Valid) -> Application/Domain 서비스 호출 -> ApiResponse로 통일 응답 반환  
- Service: @Transactional 경계에서 도메인 규칙 수행, Repository 호출, Converter로 DTO 변환
- Repository: Spring Data JPA 인터페이스 (엔티티 영속화/조회)
- 예외 흐름
  - 검증 실패: @Valid -> ConstraintViolation -> IllegalArgumentException -> @RestControllerAdvice
- 도메인/비즈니스 실패: 커스텀 예외(FoodException 등등) -> 전역 핸들러 매핑 -> 통일 에러 응답
### API 응답 통일과의 접점
- 성공 or 실패 코드와 ApiResponse 래퍼를 활용해 일관된 스펙 유지
- 전역 예외 처리는 검증, 비즈니스 모두를 받아 표준 형태로 직렬화 AOP의 한 종류
### DTO/Entity/Converter 예시
DTO들
``` java
// 요청 DTO
public class MemberReqest {
    public record JoinDTO(
            @NotBlank String name,
            @NotNull Gender gender,
            @NotNull @Past LocalDate birth,
            @NotNull Address address,
            String specAddress,
            @ExistFoods List<Long> preferCategory // 커스텀 제약
    ) {}
}

// 응답 DTO
public class MemberResponse {
    @Builder
    public record JoinDTO(
            Long memberId,
            LocalDateTime createAt
    ) {}
}
```
컨버터
``` java
// dto 컨버터
public class MemberConverter {
    public static MemberResDTO.JoinDTO toJoinDTO(Member member) {
        return MemberResDTO.JoinDTO.builder()
                .memberId(member.getId())
                .createAt(member.getCreatedAt())
                .build();
    }
    public static Member toMember(MemberReqDTO.JoinDTO dto) {
        return Member.builder()
                .name(dto.name())
                .birth(dto.birth())
                .address(dto.address())
                .detailAddress(dto.specAddress())
                .gender(dto.gender())
                .build();
    }
}
```
### Service/Repository, 트랙젝션 경계
레파지토리
``` java
public interface MemberRepository extends JpaRepository<Member, Long> {}
public interface FoodRepository extends JpaRepository<Food, Long> {
    boolean existsById(Long id);
}
public interface MemberFoodRepository extends JpaRepository<MemberFood, Long> {}
```
서비스
``` java
@Service
@RequiredArgsConstructor
public class MemberCommandServiceImpl implements MemberCommandService {

    private final MemberRepository memberRepository;
    private final MemberFoodRepository memberFoodRepository;
    private final FoodRepository foodRepository;

    @Transactional
    @Override
    public MemberResDTO.JoinDTO signup(MemberReqDTO.JoinDTO dto) {
        // 1) 회원 생성/저장
        Member member = MemberConverter.toMember(dto);
        memberRepository.save(member);

        // 2) 선호 음식 연결(검증은 DTO 단계에서 이미 수행)
        if (dto.preferCategory() != null && !dto.preferCategory().isEmpty()) {
            // 주: 스트림/for-each는 가독성/의도 중심 선택. 성능은 데이터 크기·JIT 최적화에 좌우.
            List<MemberFood> links = dto.preferCategory().stream()
                    .map(id -> MemberFood.builder()
                            .member(member)
                            .food(Food.ofId(id)) // getReferenceById 대용 정적 팩토리 예시
                            .build())
                    .toList();
            memberFoodRepository.saveAll(links);
        }

        // 3) 응답 변환
        return MemberConverter.toJoinDTO(member);
    }
}
```
컨트롤러
```
@RestController
@RequiredArgsConstructor
public class MemberController {

    private final MemberCommandService memberCommandService;

    @PostMapping("/sign-up")
    public ResponseEntity<ApiResponse<MemberResDTO.JoinDTO>> signUp(
            @Valid @RequestBody MemberReqDTO.JoinDTO dto
    ) {
        MemberResDTO.JoinDTO result = memberCommandService.signup(dto);
        return ResponseEntity
                .status(MemberSuccessCode.FOUND.getStatus())
                .body(ApiResponse.onSuccess(MemberSuccessCode.FOUND, result));
    }
}
```
### Swagger(springdoc) 세팅
``` java
@Configuration
public class SwaggerConfig {

    @Bean
    public OpenAPI openAPI() {
        String schemeName = "JWT TOKEN";
        return new OpenAPI()
            .info(new Info().title("Project")
                            .description("Project Swagger")
                            .version("0.0.1"))
            .addServersItem(new Server().url("/"))
            .addSecurityItem(new SecurityRequirement().addList(schemeName))
            .components(new Components().addSecuritySchemes(
                    schemeName, new SecurityScheme()
                        .name(schemeName)
                        .type(SecurityScheme.Type.HTTP)
                        .scheme("Bearer")
                        .bearerFormat("JWT")));
    }
}
```
- 실행 후엔 `   •   실행 후: http://localhost:8080/swagger-ui/index.html`로 접속 후 JWT 필요서 Authorize 버튼으로 베어러 토큰 입력하기
#### 의존성, 기본 OpenAPI 설정
어노테이션 기반으로 값 자체에 값 검증을 걸 수 있다.
``` java
public record JoinDTO(
        @NotBlank(message="이름은 필수입니다.") String name,
        @NotNull Gender gender,
        @NotNull @Past(message="생년월일은 과거여야 합니다.") LocalDate birth,
        @NotNull Address address,
        @Size(max=100, message="상세 주소는 100자 이하") String specAddress,
        @ExistFoods List<Long> preferCategory
) {}
```
### 어노테이션 기반 Validation
커스텀이 필요하면 다음과 같이 종속성 설정 후,
```java
implementation 'org.springframework.boot:spring-boot-starter-validation'
```
적절한 메시지를 세팅하고
``` java
@Documented
@Constraint(validatedBy = FoodExistValidator.class)
@Target({ ElementType.FIELD, ElementType.PARAMETER })
@Retention(RetentionPolicy.RUNTIME)
public @interface ExistFoods {
    String message() default "해당 음식이 존재하지 않습니다.";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```
매핑해줄 vaidator를 직접 구현한, 후
``` java
@Component
@RequiredArgsConstructor
public class FoodExistValidator implements ConstraintValidator<ExistFoods, List<Long>> {

    private final FoodRepository foodRepository;

    @Override
    public boolean isValid(List<Long> values, ConstraintValidatorContext context) {
        if (values == null || values.isEmpty()) return true; // 선택 필드인 경우

        boolean ok = values.stream().allMatch(foodRepository::existsById);
        if (!ok) {
            context.disableDefaultConstraintViolation();
            context.buildConstraintViolationWithTemplate(FoodErrorCode.NOT_FOUND.getMessage())
                   .addConstraintViolation();
        }
        return ok;
    }
}
```
controller에서 트리거를 진행해준다. 다음은 인증일때의 예시이다.
``` java
@PostMapping("/sign-up")
public ResponseEntity<ApiResponse<MemberResDTO.JoinDTO>> signUp(
        @Valid @RequestBody MemberReqDTO.JoinDTO dto
) { ... }
```
핸들러 연동은 다음과 같이 진행되게 된다.
``` java
@RestControllerAdvice
public class GeneralExceptionAdvice {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiResponse<Map<String, String>>> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new LinkedHashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(err ->
                errors.put(err.getField(), err.getDefaultMessage()));

        GeneralErrorCode code = GeneralErrorCode.VALID_FAIL;
        return ResponseEntity.status(code.getStatus())
                .body(ApiResponse.onFailure(code, errors));
    }

    // 비즈니스 예외·기타 예외 핸들러들…
}
```

### 후기

전역 예외 처리와 발리데이션, swagger 세팅 등을 위한 한걸을을 뗀 느낌이라 인상깊었던 것 같습니다....

### 미션 링크
[8주차 미션 브랜치 링크](https://github.com/hexter31376/umc_mission4/tree/study/Chapter7)