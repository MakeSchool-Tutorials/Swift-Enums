---
title: "Learn Swift by example - Part 2: Enums"
slug: enums-in-swift
---     

In [part 1 of this tutorial series](https://www.makeschool.com/tutorials/learn-swift-by-example-part-1-structs/structs-in-swift) we have discussed the power of structs in Swift (if you have missed it, you should go readit. We have seen that they often can be used instead of classes and that they can increase the maintainablity of your code. Structs are *value types* and are copied on assignment, every receiver has its own private copy that can be safely read and modified.

Today we are going to discuss a second very useful value type in Swift - `enum`. Very similar to structs, enums in Swift are a *lot* more powerful than in Objective-C.

We will start this article by taking a look how enums work in Objective-C. Then we'll move to Swift, first looking at basics use cases, then we will dive into some exciting examples of leveraging enums in Swift.

##Enums in Objetive-C

Enums in Objetive-C are pretty simple, here's an example:

    typedef NS_ENUM(NSInteger, UserType) {
        UserTypeRegular,
        UserTypeVIP,
        UserTypeAdmin
    }; 

We could use this enum to model the role of a user in an application:

    user.type = UserTypeRegular
    
This is a typical use case for an enum in Objetive-C (or pure C). Behind the scenes each of the enum entries an is mapped to an integer value (starting with 0 for the first entry). In Objective-C the above line would be equivalent to this one:

    user.type = 0
    
And that's about how much you can do with enums in Objective-C: give integer constants meaningful names. 

##Enums in Swift: Basics
 
The use case we outlined just now, modelling different user types, works pretty much the same in Swift:

    enum UserType {
      case Regular
      case VIP
      case Admin
    }

    class User {
      var type: UserType?
    }

    var user = User()

    user.type = UserType.Admin
    
In the simplest case enums in Swift can be used similar as in Objective-C. There are two interesting details about the Swift enum. 

The first is how Swift namespaces enum values. We can call the value `Regular` instead of `UserTypeRegular`. Swift automatically namespaces the member values with the name of the enum (`UserType.Regular`) while enum values in Objective-C exist in the global namespace (`UserTypeRegular`). This namespacing also allows the following short-form:

    user.type = .Admin
    
Because the compiler knows that `user.type` is declared as `UserType` we can use the name of the member value without the name of the enum.

The second interesting difference is that in Swift enum values are not mapped to integers. The type of the enum values is `UserType` and not `Int`. If you need the old Objective-C behavior, in which all members are mapped to an integer you can use enums with *Raw values* in Swift.
###Raw values 

Using raw values you can assign a value to every enum member when declaring the enum:

    enum UserType: Int {
      case Regular = 0
      case VIP = 1
      case Admin = 2
    }

To create an enum with raw values you need to declare the type after the enum name. Additionally you need to assign values of that type to each enum member. Integers are the only exception, when you don't assign explicit values Swift will automatically assign values from 0 to n:

    enum UserType: Int {
      case Regular
      case VIP
      case Admin
    }

You can access the raw value of each entry through the `rawValue` property:

    UserType.Admin.rawValue // == 2
    
Similarly you can create an enum member from a raw value:

    var userType = UserType(rawValue: 2) // == .Admin 
    
Raw values are not limited to integers. You can also use strings, characters, or floating-point numbers. If you aren't using integers you will have to explicitly assign a value to every enum member.
    
So far, so basic. I have showed you that enums in Swift can be used very similarly to enums in Objective-C. Now it's time to dive into some advanced features!

##Enums in Swift: Advanced Features

Enums in Swift have a bunch of features they didn't have in Objective-C. Enums can have initializers, methods, computed properties and they can even conform to protocols! We're going to start by exploring another feature, called *Associated Values* because I believe it is the most important enum feature in Swift.

###Associated Values
In Objetive-C we could already associate values with enum members by storing an integer value for each enum entry, as shown at the beginning of this tutorial. So what is new about this?

Firstly, associated values allow each enum member to store data of a any type. One enum member can store a string while another member stores an integer, etc. In Objective-C and when working with raw value enums in Swift, all members need to have the same type (e.g. integer).

Secondly, associated values are assigned when the enum value is *created*, not when the enum is declared. In the young history of the Swift language this feature has most commonly been used to model the return result of functions. Let's dive into a larger example to explain associated values. 

We want to model a bank account. The bank account has a cash amount associated with it. It also has a method to withdraw money. If the withdrawal amount is larger than the cash amount, the transaction fails and we want to return an error message to the caller. If there's enough cash in the account, the money get's withdrawn and we want to return the new balance to the caller. Depending on success or failure of the transaction we want to return a value with a different type.

It turns out that this can be modelled very nicely with an enum that has associated values:

    enum WithdrawalResult {
      case Success(Int)
      case Error(String)
    } 

As you can see in this example, each enum member can have its own associated type. We model each of the possible outcomes as one enum member. 

Next, let's set up the bank account with the cash balance and a withdrawal function:

    struct BankAccount {
      var cash = 100
      
      mutating func withdraw(amount:Int) -> WithdrawalResult {
        if (amount <= cash) {
          cash -= amount
          return .Success(cash)
        } else {
          return .Error("Not enough money!")
        }
      }
    }

The withdrawal fuction takes an amount to withdraw and returns a value of type `WithdrawalResult`. 

If you want to learn more about Swift and ship your own original iPhone App or iPhone Game you should attend our [Summer Academy](https://makeschool.com/apply?referrer=54750)!

Stay tuned for more tutorials of this series!
