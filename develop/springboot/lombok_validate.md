```java
@Data
public class Member {

private Long idx;

@NotNull(message="name null")
private String name;

@Min(value=14, message="min 14")
private Integer age;

@NotNull(message="tel null")
private String tel;
}
@NotNull: null 검증
@Min, @Max: 최소값, 최대값 검증
@Size: 범위 검증
@Email: e-mail 검증
@AssertTrue: true 검증


@NotEmpty: null이나 size가 0 검증 (String, Collection)
@NotBlank: null이나 whitespace 검증 (String)
@Positive, @PositiveOrZero: 숫자 검증
@Negative, @NegativeOrZero: 숫자 검증
@Past, @PastOrPresent: 날짜 검증
@Future, @FutureOrPresent: 날짜 검증

@pattrn
정규식 pattrn
날짜  YYYY-MM-DD
var dayRegExp = /^(19|20)\d{2}-(0[1-9]|1[012])-(0[1-9]|[12][0-9]|3[0-1])$/;
시간  HH24:mm (24시간)
var timeRegExp = /^([1-9]|[01][0-9]|2[0-3]):([0-5][0-9])$/;
복합 YYYYMMDD HH24:mm (중간 공백)
var RegExp = /^(19|20)\d{2}(0[1-9]|1[012])(0[1-9]|[12][0-9]|3[0-1])\s([1-9]|[01][0-9]|2[0-3]):([0-5][0-9])$/;
```

