# Never Type

**Never type** — это тип, не имеющий ни одного значения, то есть по сути является
пустым множеством. В язык программирования используется для обозначения невозможности 
вычисления (его завершения) и зачастую является [bottom-типом](https://en.wikipedia.org/wiki/Bottom_type). 

## Примеры использования 

### Базовый пример

Например, у нас есть функция `exit`, что завершает исполнения нашей программы, 
то есть функция никогда не завершиться! Вот реальная сигнатура `std::process::exit` 
из стандартной библиотеке Rust[^neverust]'а:
```rust
pub fn exit(code: i32) -> !
```
* `!` - литерал never-типа


### Инвариант типов

```rust
fn main() -> Result<!, ExitCode> {
  // ...
}
```

## Альтернативы 

В ML языках (например Haskell) так таковой never-тип не используется, сигнатура 
той же функции `exit` в стандартной библиотеке Haskell'я выглядит так:
```haskell
exitWith :: ExitCode -> IO a
```
* `a` - переменная типа 

Здесь используется return-type полиморфизм, что с точки зрения типов будет иметь
похожий эффект как и never-тип.
```haskell
isOneOrDie :: Int -> IO String
isOneOrDie 1 = pure "ok"
isOneOrDie _ = die "💀"
```

## Ссылки 

* StackOverflow [Why does Rust have a "Never" primitive type?](https://stackoverflow.com/a/51835567)
* [Rust never type](https://doc.rust-lang.org/stable/std/primitive.never.html)
* Хабр ["TypeScript. Мощь never"](https://habr.com/ru/articles/471026/)

---
[^neverust]: На момент весны 2023 года, `rustc 1.69.0`, never type в Rust не является first-class типом, то есть его нельзя использовать в композиции с другими типами. Например: `Result<!, E>`. Однако, это верно только для стабильной версии компилятора, так как nightly имеют экспериментальную поддержку.   
