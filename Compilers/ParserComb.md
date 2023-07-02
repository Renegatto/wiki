# Парсеры-комбинаторы 

**Парсеры-комбинаторы** — это функциональный подход к написанию $LL(k)$ парсеров, по сути является всё тем же рекурсивным спуском. В общем подход основан на *композиции* простых парсеров в более сложные. 

## Классификация

Парсеры-комбинаторы бывают двух основных видов:
* **scanner-full** — есть лексический анализ, парсеры работают с токенами
* **scanner-less** — нету лексического анализа, парсеры работают с символьной последовательностью 

Зачастую *scanner-full* парсеры куда удобнее при разборе, например, языков программирования, но куда менее поворотливы в отличие от *scanner-less*, где мы не ограничены токенами и можем разбирать строку по-разному для разных случаев. 

## Слабые стороны 

* Язык программирования должен располагать функциями высшего порядка, замыканиями
* Вывод ошибок может оказаться не особо тривиальным в сравнение с простыми методом рекурсивного спуска
* ...

## Иллюстрация

Для иллюстрации процесса воспользуемся Rust'ом и TypeScript'ом, ну и теоретическим примером: нам надо разобрать число в строке. Грамматика числа следующая (в [PEG](https://ru.wikipedia.org/wiki/%D0%93%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%D1%82%D0%B8%D0%BA%D0%B0,_%D1%80%D0%B0%D0%B7%D0%B1%D0%B8%D1%80%D0%B0%D1%8E%D1%89%D0%B0%D1%8F_%D0%B2%D1%8B%D1%80%D0%B0%D0%B6%D0%B5%D0%BD%D0%B8%D0%B5) нотации): 
```
Number <- ('+' | '-')? Digits ('.' Digits)?
Digits <- [0-9]+
```
Примеры:
```json
-2
2.3
+5.0
```
Если мы внимательнее взглянем на грамматику, то увидим, что даже в контексте одного правила (*нетерминального символа*) мы можем увидеть *под части*, *под правила*, что в итоге обедняются с помощью уже упомянутой композицией. 

### scanner-less 

В этом примере воспользуемся Rust'ом и крейтом [`nom`](https://docs.rs/nom/latest/nom/) (но мы намеренно будем не большей частью функционала ради наглядности). На этом моменте предполагается достойное знания Rust'а!

Хоть в scanner-less парсерах-комбинаторах мы и не работает с токенами, токен это весьма удобная концепция и мы будем использовать её как паттерн. В нашем случае под *паттерн токена* (давайте так это называть) попадает правило `Digits`:
```rust
/// Digit <- [0-9]
fn digit(input: char) -> bool {
    input >= '0' && input <= '9'
}

/// Digits <- Digit+
fn digits(input: &str) -> IResult<&str, &str> {
    take_while1(digit)(input)
}

```

```rust
#[test]
fn test_digits() {
    assert_eq!(digits("12c3"), Ok(("c3", "12")));
}
```

Разбор знака:
```rust
enum Sign {
    Plus,
    Minus,
}

/// Sign <- '+' | '-'
fn sign(input: &str) -> IResult<&str, Sign> {
    let (input, sign) = alt((tag("+"), tag("-")))(input)?;
    let sign = if sign == "+" { Sign::Plus } else { Sign::Minus };

    Ok((input, sign))
}
```

```rust
#[test]
fn test_sign() {
    assert_eq!(sign("+"), Ok(("", Sign::Plus)));
    assert_eq!(sign("-"), Ok(("", Sign::Minus)));
}
```

Финал, разбор `Number`:
```rust
struct Number<'a> {
    sign: Option<Sign>,
    integer: &'a str,
    fraction: Option<&'a str>,
}

/// Number <- Sign? Digits ("." digits)?
fn number(input: &str) -> IResult<&str, Number> {
    let fraction_parser = map(tuple((tag("."), digits)), |(_, frac)| frac);

    let (input, (sign, integer, fraction)) =
        tuple((opt(sign), digits, opt(fraction_parser)))(input)?;

    Ok((
        input,
        Number {
            sign,
            integer,
            fraction,
        },
    ))
}
```

```rust
#[test]
fn test_number() {
    assert_eq!(
        number("12c3"),
        Ok((
            "c3",
            Number {
                sign: None,
                integer: "12",
                fraction: None
            }
        ))
    );
}
```

### scanner-full

Лень... смотрите [`ts-parsec`](https://github.com/microsoft/ts-parsec).
