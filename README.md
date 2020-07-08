# 타입의 의미
코틀린의 널이 될 수 있는 타입은 널에 대한 문제에 대해 종합적인 해법을 제공한다.    
널이 될 수 있는 타입과 널이 될 수 없는 타입을 구분하면 각 타입의 값에 대해 어떤 연산이 가능할지 명확하게 이해할 수 있고, 실행 시점에 예외를 발생시킬 수 있는 연산을 판단할 수 있다.   
실행 시점에 널이 될 수 있는 타입이나 널이 될 수 없는 타입의 객체는 같다.   
널이 될 수 있는 타입은 널이 될 수 없는 타입을 감싼 래퍼 타입이 아니다.   
모든 검사는 컴파일 시점에 수행된다. 따라서 코틀린에서는 널이 될 수 있는 타입을 처리하는 데 별도의 실행시점 부가 비용이 들지 않는다.   

# 안전한 호출 연산자: ?.
코틀린이 제공하는 유용한 도구 중 하나가 안전한 호출 연산자인 ?.이다. 이 연산자는 null 검사와 메소드 호출을 한 번의 연산으로 수행한다.   
호출하려는 값이 null이면 이 호출은 무시되고 null이 결과 같이 된다. 하지만 이 연산자의 결과 타입도 널이 될 수 있는 타입이라는 것을 유의해야 한다.

<pre>
<code>
fun printAllCaps(s: String?) {
  val allCaps: String? = s?.toUpperCase()
  println(allCaps)
}

>>>printAllCaps("abc")
ABC
>>>printAllCaps(null)
null
</code>
</pre>

메소드 뿐만 아니라 프로퍼티를 읽거나 쓸 때도 안전한 호출 연산자를 사용할 수 있는데, 다음 예제는 널이 될 수 있는 프로퍼티가 있는 간단한 코틀린 클래스로 프로퍼티 접근 시 안전한 호출을 하는 방법을 보여준다.

<pre>
<code>
class Employee(val name: String, val manager: Employee?)
fun managerName(employee: Employee): String? = employee.manager?.name
>>> val ceo = Employee("Da Boss", null)
>>> val developer = Employee("Bob Smith", ceo)
>>> println(managerName(developer))
Bob Smith
>>> println(managerName(ceo))
null
</code>
</pre>

널이 될 수 있는 중간 객체가 여럿 있다면 한 식 안에서 안전한 호출을 연쇄해서 함께 사용하면 좀 더 편하게 만들 수 있다.

<pre>
<code>
class Address(val streetAddress: String, val zipCode: Int, val city: String, val country: String)
class Company(val name: String, val address: Address?)
class Person(val name: String, val company: Company?)

fun Person.countryName(): String{
  val country = this.company?.address?.country
  return if (country != null) country else "Unknown"
}

>>>val person = Person("Dimitry", null)
>>>println(person.countryName())
Unknown
</code>
</pre>

코틀린에서는 이런식으로 짧은 식으로 널 검사를 할 수 있다. 하지만 위 코드에서는 불필요하게도  country가 null인지 검사해서 정상적으로 얻은 country 값을 반환하거나 UnKnown 을 반환할때 if문을 사용하는데 엘비스 연산자를 사용하면 이 마저도 생략이 가능하다.
