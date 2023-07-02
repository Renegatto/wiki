# Row-полиморфизм

Ссылки на Википедию:
* [Рядный полиморфизм Ванда](https://ru.m.wikipedia.org/wiki/%D0%9F%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B8%D0%B9_%D0%BF%D0%BE%D0%BB%D0%B8%D0%BC%D0%BE%D1%80%D1%84%D0%B8%D0%B7%D0%BC#%D0%A0%D1%8F%D0%B4%D0%BD%D1%8B%D0%B9_%D0%BF%D0%BE%D0%BB%D0%B8%D0%BC%D0%BE%D1%80%D1%84%D0%B8%D0%B7%D0%BC_%D0%92%D0%B0%D0%BD%D0%B4%D0%B0)
* [Row polymorphism](https://en.wikipedia.org/wiki/Row_polymorphism)

Ссылки на другие статьи:
* [Row polymorphism crash course](https://ahnfelt.medium.com/row-polymorphism-crash-course-587f1e7b7c47)

## Примеры

```ocaml
(* OCaml *)

let get_x r = r#x 
                
let foo =
  object
    method x = 123
  end;;
    
print_int @@ get_x foo
```