<!-- TOC -->
* [Basic](#basic)
  * [1. Какие типы тестов вы знаете и какие проблемы они решают?](#1-какие-типы-тестов-вы-знаете-и-какие-проблемы-они-решают)
  * [2. Каковы ключевые различия между ними?](#2-каковы-ключевые-различия-между-ними)
* [Regular](#regular)
  * [3. Какие паттерны/техники позволяют изолировать зависимости в ваших тестах?](#3-какие-паттернытехники-позволяют-изолировать-зависимости-в-ваших-тестах)
  * [4. В чем разница между mock и stub? А fake?](#4-в-чем-разница-между-mock-и-stub-а-fake)
* [Advanced](#advanced)
  * [5. Как можно изолировать сторонние зависимости (например, REST/gRPC/email/и т. д.) в ваших тестах?](#5-как-можно-изолировать-сторонние-зависимости-например-restgrpcemailи-т-д-в-ваших-тестах)
  * [6. Какой подход к разработке программного обеспечения позволяет писать тестируемый код с самого начала?](#6-какой-подход-к-разработке-программного-обеспечения-позволяет-писать-тестируемый-код-с-самого-начала)
  * [7. Как TDD позволяет писать лучший код?](#7-как-tdd-позволяет-писать-лучший-код)
  * [8. Объясните подход/поток TDD.](#8-объясните-подходпоток-tdd)
  * [9. Какова цель BDD?](#9-какова-цель-bdd)
<!-- TOC -->

# Basic

## 1. Какие типы тестов вы знаете и какие проблемы они решают?

**Ответ:**

Существует несколько типов тестов, каждый из которых решает свои специфические задачи:

- **Модульные тесты (Unit Tests):** Тестируют отдельные модули или функции. Они помогают обнаружить и исправить ошибки на ранней стадии разработки и обеспечивают проверку правильности работы отдельных частей кода.
- **Интеграционные тесты (Integration Tests):** Тестируют взаимодействие между различными модулями или компонентами системы. Они помогают убедиться, что отдельные части кода работают правильно вместе.
- **Системные тесты (System Tests):** Тестируют всю систему в целом. Эти тесты помогают проверить, что вся система работает правильно в соответствии с требованиями.
- **Приемочные тесты (Acceptance Tests):** Проверяют систему на соответствие требованиям пользователя. Эти тесты помогают убедиться, что система удовлетворяет потребности конечных пользователей.
- **Регрессионные тесты (Regression Tests):** Проверяют, что изменения в коде не привели к появлению новых ошибок. Эти тесты помогают сохранить качество продукта при внесении изменений.

## 2. Каковы ключевые различия между ними?

**Ответ:**

Ключевые различия между типами тестов заключаются в следующем:

- **Масштаб:**
  - Модульные тесты охватывают небольшие части кода (функции, методы).
  - Интеграционные тесты охватывают несколько модулей, работающих вместе.
  - Системные тесты охватывают всю систему целиком.
  - Приемочные тесты проверяют соответствие системы требованиям пользователя.

- **Цель:**
  - Модульные тесты направлены на выявление ошибок в отдельных модулях.
  - Интеграционные тесты направлены на проверку взаимодействия между модулями.
  - Системные тесты проверяют функциональность всей системы.
  - Приемочные тесты проверяют соответствие требованиям пользователя.
  - Регрессионные тесты проверяют, что изменения не нарушили существующую функциональность.

- **Время выполнения:**
  - Модульные тесты обычно выполняются быстро.
  - Интеграционные и системные тесты занимают больше времени.
  - Приемочные тесты могут быть наиболее длительными.

# Regular

## 3. Какие паттерны/техники позволяют изолировать зависимости в ваших тестах?

**Ответ:**

Для изоляции зависимостей в тестах используются следующие паттерны и техники:

- **Mock объекты:** Используются для имитации поведения сложных объектов. Они помогают изолировать тестируемый код от его зависимостей.
- **Stub объекты:** Предоставляют предопределенные ответы на вызовы. Они используются для упрощения тестирования, когда реальный объект слишком сложен или недоступен.
- **Fake объекты:** Реализуют интерфейсы зависимостей, но с упрощенной логикой. Они используются, когда необходимо заменить реальную зависимость на тестовую реализацию.
- **Dependency Injection (DI):** Позволяет передавать зависимости в тестируемый код через конструкторы или сеттеры. Это облегчает замену реальных зависимостей на тестовые.

## 4. В чем разница между mock и stub? А fake?

**Ответ:**

- **Mock:** Это объекты, которые используются для проверки взаимодействия между тестируемым объектом и его зависимостями. Они позволяют задавать ожидания на вызовы методов и проверять их выполнение.
- **Stub:** Это объекты, которые предоставляют предопределенные ответы на вызовы методов. Они не проверяют взаимодействие, а только предоставляют данные для тестов.
- **Fake:** Это объекты, которые полностью реализуют интерфейс зависимостей, но с упрощенной логикой. Они используются для замены реальных зависимостей на тестовые реализации.

# Advanced

## 5. Как можно изолировать сторонние зависимости (например, REST/gRPC/email/и т. д.) в ваших тестах?

**Ответ:**

Для изоляции сторонних зависимостей можно использовать:

- **Mock серверы:** Запускать локальные серверы, которые имитируют поведение реальных сервисов (например, REST или gRPC). Это позволяет тестировать взаимодействие с внешними сервисами без их реального вызова.
- **Stub серверы:** Создавать серверы, которые возвращают предопределенные ответы на запросы. Это помогает тестировать код, не завися от реальных внешних сервисов.
- **Фиктивные объекты:** Использовать объекты, которые реализуют интерфейсы внешних сервисов с упрощенной логикой. Это позволяет тестировать код без обращения к реальным внешним сервисам.

Пример использования mock сервера для REST API в Go с помощью библиотеки `httptest`:

```go
package main

import (
    "net/http"
    "net/http/httptest"
    "testing"
)

func TestHandler(t *testing.T) {
    req, err := http.NewRequest("GET", "/endpoint", nil)
    if err != nil {
        t.Fatal(err)
    }

    rr := httptest.NewRecorder()
    handler := http.HandlerFunc(myHandler)
    handler.ServeHTTP(rr, req)

    if status := rr.Code; status != http.StatusOK {
        t.Errorf("handler returned wrong status code: got %v want %v",
            status, http.StatusOK)
    }

    expected := `{"message":"hello world"}`
    if rr.Body.String() != expected {
        t.Errorf("handler returned unexpected body: got %v want %v",
            rr.Body.String(), expected)
    }
}
```

## 6. Какой подход к разработке программного обеспечения позволяет писать тестируемый код с самого начала?

**Ответ:**

**Разработка через тестирование (Test-Driven Development, TDD)** позволяет писать тестируемый код с самого начала. В TDD разработчики сначала пишут тесты для новых функций или исправлений, а затем пишут код, который проходит эти тесты. Это обеспечивает высокое покрытие тестами и гарантирует, что код изначально разработан с учетом тестируемости.

## 7. Как TDD позволяет писать лучший код?

**Ответ:**

TDD позволяет писать лучший код за счет:

- **Раннего выявления ошибок:** Тесты пишутся до кода, что помогает выявить ошибки на ранней стадии.
- **Улучшения дизайна кода:** Писать тесты для кода, который сложно тестировать, трудно. Это стимулирует разработчиков писать код с четким разделением обязанностей и слабой связанностью.
- **Поддержки качества кода:** Постоянное написание тестов и рефакторинг кода улучшают его качество и облегчают дальнейшее сопровождение.
- **Документации:** Тесты служат живой документацией кода, показывая, как он должен работать.

## 8. Объясните подход/поток TDD.

**Ответ:**

Подход TDD включает следующие шаги:

1. **Написание теста:** Написание небольшого теста, который проверяет новую функцию или улучшение. Тест на этом этапе не проходит.
2. **Реализация кода:** Написание минимального объема кода, необходимого для прохождения теста.
3. **Запуск теста:** Запуск теста и убедиться, что он

проходит.
4. **Рефакторинг:** Улучшение кода без изменения его функциональности и повторное выполнение теста, чтобы убедиться, что он все еще проходит.

Этот цикл повторяется для каждой новой функции или улучшения, что приводит к созданию хорошо протестированного и легко сопровождаемого кода.

## 9. Какова цель BDD?

**Ответ:**

Цель **разработки через поведение (Behavior-Driven Development, BDD)** заключается в улучшении общения между разработчиками, тестировщиками и бизнес-аналитиками. BDD фокусируется на спецификациях поведения системы, написанных на языке, понятном всем участникам процесса разработки. Это помогает обеспечить, чтобы все стороны имели общее понимание требований и того, как система должна работать.

Пример сценария на языке Gherkin:

```gherkin
Feature: User login

  Scenario: Successful login
    Given a user "John" with password "password123"
    When the user attempts to login with username "John" and password "password123"
    Then the login should be successful
    And the user should be redirected to the dashboard
```
