---
layout: single
title: "[프로그래밍 규칙] 1주차 실행하기"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

## 1. 단순한 코드를 짜자! 일차원적으로 한 메서드의 길이를 줄이기 시도

이펙티브 코틀린 2단계 미션 첫 번째 구현
``` kotlin
class FourBasicOperations {
    fun calculate(expression: String): Int {
        if (expression.isNullOrBlank()) {
            throw IllegalArgumentException("입력값이 null이거나 빈 공백 문자입니다.")
        }

        val cleanedExpression = expression.replace(" ", "")
        val numbers = mutableListOf<Int>()
        val operators = mutableListOf<Char>()
        var currentNumber = StringBuilder()

        for (char in cleanedExpression) {
            when {
                char.isDigit() -> currentNumber.append(char)
                char in listOf('+', '-', '*', '/') -> {
                    if (currentNumber.isEmpty()) {
                        throw IllegalArgumentException("입력값이 잘못되었습니다.")
                    }

                    numbers.add(currentNumber.toString().toInt())
                    currentNumber = StringBuilder()
                    operators.add(char)
                }
                else -> throw IllegalArgumentException("사칙연산 기호가 아닙니다.")
            }
        }

        if (currentNumber.isNotEmpty()) {
            numbers.add(currentNumber.toString().toInt())
        } else if (operators.isNotEmpty()) {
            throw IllegalArgumentException("입력값이 잘못되었습니다.")
        }

        return evaluate(numbers, operators)
    }

    private fun evaluate(
        numbers: List<Int>,
        operators: List<Char>,
    ): Int {
        val values = numbers.toMutableList()
        val ops = operators.toMutableList()

        var i = 0
        while (i < ops.size) {
            if (ops[i] == '*' || ops[i] == '/') {
                val left = values[i]
                val right = values[i + 1]
                val result = if (ops[i] == '*') left * right else left / right
                values[i] = result
                values.removeAt(i + 1)
                ops.removeAt(i)
            } else {
                i++
            }
        }

        var total = values[0]
        for (j in 0 until ops.size) {
            total = if (ops[j] == '+') total + values[j + 1] else total - values[j + 1]
        }

        return total
    }
}
```
한 메서드 당 길이가 너무 길다고 생각하여 메서드 분리, 메시지 상수 처리
``` kotlin
class FourBasicOperations {
    private val nullInputErrorMessage: String = "입력값이 null이거나 빈 공백 문자입니다."
    private val badInputErrorMessage: String = "입력값이 잘못되었습니다."
    private val notSupportedOperatorErrorMessage: String = "사칙연산 기호가 아닙니다."

    fun calculate(expression: String): Int {
        if (expression.isNullOrBlank()) {
            throw IllegalArgumentException(nullInputErrorMessage)
        }

        val cleanedExpression = expression.replace(" ", "")
        val numbers = mutableListOf<Int>()
        val operators = mutableListOf<Char>()
        var currentNumber = StringBuilder()

        parseExpression(cleanedExpression, numbers, operators, currentNumber)
        finalizeNumbers(numbers, operators, currentNumber)

        return evaluate(numbers, operators)
    }

    private fun parseExpression(
        expression: String,
        numbers: MutableList<Int>,
        operators: MutableList<Char>,
        currentNumber: StringBuilder,
    ) {
        for (char in expression) {
            when {
                char.isDigit() -> currentNumber.append(char)
                char in listOf('+', '-', '*', '/') -> {
                    if (currentNumber.isEmpty()) {
                        throw IllegalArgumentException(badInputErrorMessage)
                    }
                    numbers.add(currentNumber.toString().toInt())
                    currentNumber.clear()
                    operators.add(char)
                }
                else -> throw IllegalArgumentException(notSupportedOperatorErrorMessage)
            }
        }
    }

    private fun finalizeNumbers(
        numbers: MutableList<Int>,
        operators: MutableList<Char>,
        currentNumber: StringBuilder,
    ) {
        if (currentNumber.isNotEmpty()) {
            numbers.add(currentNumber.toString().toInt())
        } else if (operators.isNotEmpty()) {
            throw IllegalArgumentException(badInputErrorMessage)
        }
    }

    private fun evaluate(
        numbers: List<Int>,
        operators: List<Char>,
    ): Int { (생략) }
}
```

## 2. 단순히 섹션 단위로 나누는 것이 아니라 역할을 생각하자!

첫 번째 코드 리뷰에서 finalizeNumbers 메서드의 역할이 무엇이냐는 질문을 받음! 생각해보니...? 아무생각없이 나눈 나! <br />
로직 안에서의 역할을 숫자찾기, 연산자 찾기로 분리하고 메서드를 분리함, 사칙 연산은 Enum으로 분리
``` kotlin
class FourBasicOperations {
    private val nullInputErrorMessage: String = "입력값이 null이거나 빈 공백 문자입니다."
    private val badInputErrorMessage: String = "입력값이 잘못되었습니다."
    private val notSupportedOperatorErrorMessage: String = "사칙연산 기호가 아닙니다."

    fun calculate(expression: String): Int {
        if (expression.isNullOrBlank()) {
            throw IllegalArgumentException(nullInputErrorMessage)
        }

        val cleanedExpression = expression.replace(" ", "")
        val numbers = parseNumbers(cleanedExpression)
        val operators = parseOperators(cleanedExpression)

        return evaluate(numbers, operators)
    }

    private fun parseNumbers(expression: String): List<Int> {
        val numbers = mutableListOf<Int>()
        val currentNumber = StringBuilder()
        
        for (char in expression) {
            if (char.isDigit()) {
                currentNumber.append(char)
            } else if (char != ' ') {
                if (currentNumber.isNotEmpty()) {
                    numbers.add(currentNumber.toString().toInt())
                    currentNumber.clear()
                }
            }
        }
        
        if (currentNumber.isNotEmpty()) {
            numbers.add(currentNumber.toString().toInt())
        }
        
        return numbers
    }

    private fun parseOperators(expression: String): List<Operator> {
        val operators = mutableListOf<Operator>()
        val currentNumber = StringBuilder()
        val operatorList = Operator.entries.map { it.symbol }

        for (char in expression) {
            if (char.isDigit()) {
                currentNumber.append(char)
            } else if (operatorList.contains(char.toString())) {
                if (currentNumber.isEmpty()) {
                    throw IllegalArgumentException(badInputErrorMessage)
                }
                operators.add(Operator.from(char.toString()))
                currentNumber.clear()
            } else {
                throw IllegalArgumentException(notSupportedOperatorErrorMessage)
            }
        }

        return operators
    }

    private fun evaluate(
        numbers: List<Int>,
        operators: List<Char>,
    ): Int { (생략) }
}
```
``` kotlin
enum class Operator(val symbol: String) {
    SUM("+"),
    SUBTRACT("-"),
    MULTIPLY("*"),
    DIVIDE("/"),
    ;

    companion object {
        fun from(symbol: String): Operator {
            return entries.find { it.symbol == symbol }
                ?: throw IllegalArgumentException("지원하지 않는 연산자입니다.")
        }
    }
}
```

## 3. 최대한 관련된 것들 끼리 묶어놓자!

사칙 연산에 대한 정의를 Enum에서 정의하고 사용
``` kotlin
enum class Operator(val symbol: String, val operation: (Int, Int) -> Int) {
    SUM("+", { a, b -> a + b }),
    SUBTRACT("-", { a, b -> a - b }),
    MULTIPLY("*", { a, b -> a * b }),
    DIVIDE("/", { a, b ->
        if (b == 0) throw IllegalArgumentException("0으로 나눌 수 없습니다.")
        a / b
    }),
    ;

    companion object {
        fun from(symbol: String): Operator {
            return entries.find { it.symbol == symbol }
                ?: throw IllegalArgumentException("지원하지 않는 연산자입니다.")
        }
    }
}
```
``` kotlin
private fun evaluate(
    numbers: List<Int>,
    operators: List<Operator>,
): Int {
    val values = numbers.toMutableList()
    val ops = operators.toMutableList()

    var i = 0
    while (i < ops.size) {
        if (ops[i] == Operator.MULTIPLY || ops[i] == Operator.DIVIDE) {
            val result = ops[i].operation(values[i], values[i + 1])
            values[i] = result
            values.removeAt(i + 1)
            ops.removeAt(i)
        } else {
            i++
        }
    }

    var total = values[0]
    for (j in 0 until ops.size) {
        total = ops[j].operation(total, values[j + 1])
    }
    return total
}
```

## 4. 단순한 코드의 끝은 역할 분리...?!
- 단순한 코드니까 짧게 나누자
- 무작정 나누니까 이상하다 역할 별로 나누자
- 역할 별로 나누니까 다른 클래스에 있어야 될 것들도 보인다


이런 흐름으로 하게 되어서 메서드의 역할에 집중하고 구현하는 습관을 들이면 좋을 것 같다! 그것이 바로 객체지향....?!