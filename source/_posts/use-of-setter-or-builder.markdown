---
layout: post
title: "SetterとBuilderの使いわけ"
date: 2018-06-29 17:39:49 +0900
comments: true
categories: Java
---

先日、「SetterとBuilderはどのように使いわければいいのか？」という質問を受けました。なかなかよい質問ですね。Builderを使うとクラスのインスタンスを柔軟に作ることができます。Builderを使ったサンプルコードです。

```java
User user = User.builder()
        .lastName("山田")
        .firstName("太郎")
        .build();
```

Builderを実装したUserクラスのサンプルコードです。

```java
public class User {
    private final String lastName;
    private final String firstName;

    User(String lastName, String firstName) {
        this.lastName = lastName;
        this.firstName = firstName;
    }

    public static UserBuilder builder() {
        return new UserBuilder();
    }

    public static class UserBuilder {
        private String lastName;
        private String firstName;

        UserBuilder() {
        }

        public UserBuilder lastName(String lastName) {
            this.lastName = lastName;
            return this;
        }

        public UserBuilder firstName(String firstName) {
            this.firstName = firstName;
            return this;
        }

        public User build() {
            return new User(lastName, firstName);
        }
    }
}
```

Setterを使うと次のようになります(実装は省略)。

```java
User user = new User();
user.setLastName("山田");
user.setFirstName("太郎");
```

Setterの問題点として、インスタンスの状態を変更してしまうことがあります。イミュータブルな実装にする場合、原則としてコンストラクタで値は設定するべきでSetterで変更するべきではありません。


では、Setterではなくコンストラクタで設定するようにしましょう。引数が少ないうちは問題がないのですが、次の例のように引数の数が増えてしまうとコードが煩雑になってしまいます。

```java
// 姓、名、年齢、血液型、国が引数のコンストラクタの例
// 年齢と血液型は不明だが、nullを指定する必要がある
User user = new User("山田", "太郎", null, null, "日本");
```

そこで、Builderを使うメリットが出てきます。Builderを使えば柔軟にパラメーターの設定が行えるようになります。

```java
User user = User.builder()
        .lastName("山田")
        .firstName("太郎")
        .country("日本")
        .build();
```

SetterもBuilderもやりたいことはオブジェクトにパラメーターをセットすることです。Setterを使うとオブジェクトの状態を変更してしまうことになります。イミュータブルな実装かつ柔軟にパラメーターを設定したい場合はBuilderを使いましょう。
