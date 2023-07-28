## 워드프레스 함수 작성을 위함
### 연봉실 수령액
```php
function calculate_take_home_pay($atts) {
    $a = shortcode_atts( array(
        'salary' => 0,
        'num_children' => 0,
        'num_dependents' => 0,
    ), $atts );

    // calculate deductions
    $pension_deduction = $a['salary'] * 0.045; // 국민연금
    $health_insurance_deduction = $a['salary'] * 0.0323; // 건강보험료
    $employment_insurance_deduction = $a['salary'] * 0.00325; // 고용보험
    $total_deductions = $pension_deduction + $health_insurance_deduction + $employment_insurance_deduction;

    // calculate income tax based on progressive tax rate
    $income_tax = 0;
    if ($a['salary'] <= 12000000) {
        $income_tax = $a['salary'] * 0.06;
    } else if ($a['salary'] <= 46000000) {
        $income_tax = 720000 + ($a['salary'] - 12000000) * 0.15;
    } else if ($a['salary'] <= 88000000) {
        $income_tax = 5820000 + ($a['salary'] - 46000000) * 0.24;
    } else if ($a['salary'] <= 150000000) {
        $income_tax = 15900000 + ($a['salary'] - 88000000) * 0.35;
    } else if ($a['salary'] <= 500000000) {
        $income_tax = 37600000 + ($a['salary'] - 150000000) * 0.38;
    } else {
        $income_tax = 176600000 + ($a['salary'] - 500000000) * 0.4;
    }

    // calculate deduction for dependents and children
    $dependent_deduction = $a['num_dependents'] * 1500000; // 기본적으로 부양가족 1인당 150만원 공제
    $children_deduction = $a['num_children'] * 1500000; // 기본적으로 자녀 1인당 150만원 공제
    $total_deductions += $dependent_deduction + $children_deduction;

    // subtract deductions from salary
    $net_salary = $a['salary'] - $total_deductions - $income_tax;

    return $net_salary;
}
add_shortcode('take_home_pay', 'calculate_take_home_pay');

```

위의 코드를 추가한 후에는 워드프레스 페이지나 포스트에서 아래와 같이 새로운 단축 코드를 사용할 수 있습니다.
```
[take_home_pay salary="50000000" num_children="2" num_dependents="1"]
```