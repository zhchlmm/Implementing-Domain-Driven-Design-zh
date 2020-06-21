# 第 6 章 值对象

> Chapter 6. Value Objects

Price is what you pay. Value is what you get.

—Warren Buffett

Although often overshadowed by entity-think, Value Objects are a vital building block of DDD. Examples of objects that are commonly modeled as Values are numbers, such as 3, 10, and 293.51; text strings, such as “hello, world!” and “Domain-Driven Design”; dates; times; more detailed objects such as a person’s full name composed of first, middle, last name, and title attributes; and others such as currency, colors, phone numbers, and postal addresses. And there are more complex kinds. I’ll be discussing Values that model concepts of your domain using your Ubiquitous Language (1), addressing the goals of Domain-Driven Design.

::: tip
Know the Value Advantages

Value types that measure, quantify, or describe things are easier to create, test, use, optimize, and maintain.
:::

It may surprise you to learn that we should strive to model using Value Objects instead of Entities wherever possible. Even when a domain concept must be modeled as an Entity, the Entity’s design should be biased toward serving as a Value container rather than a child Entity container. That advice is not based on an arbitrary preference. Value types that measure, quantify, or describe things are easier to create, test, use, optimize, and maintain.

::: tip
Road Map to This Chapter

- Learn how to understand the characteristics of a domain concept to model as a Value.
- See how to leverage Value Objects to minimize integration complexity.
- Examine the use of domain Standard Types expressed as Values.
- Consider how SaaSOvation learned the importance of Values.
- Learn how the SaaSOvation teams tested, implemented, and persisted their Value types.

:::

::: tip
![](./figures/ch6/team_meeting-2.jpg)

At first the SaaSOvation teams went overboard with their use of Entities. This actually started to happen well before the User and Permission concepts got intertwined with collaboration. From project inception they followed the popular mode of thinking that every element of their domain model needed to map to its own database table, and that all their attributes should be easily set and retrieved through public accessor methods. Since every object had a database primary key, the model was tightly stitched together into a large, complex graph. That idea primarily came from the data modeling perspective that most developers have when unduly influenced by relational databases, where everything is normalized and referenced using foreign keys. As they later learned, getting caught in the tide of entity-think was not only unnecessary, it was also more costly in development time and effort.
:::

When designed correctly, a Value instance can be created, handed off, and forgotten about. We don’t have to worry that the consumer has somehow modified it incorrectly, or even modified it at all. A Value can have a brief or long existence. It’s just an unharmed and harmless Value that comes and goes as needed.

This is a huge load off of our mind, similar to transitioning from a programming language without managed memory facilities to one with garbage collection. With the ease of use that Values afford, we should want as much of their kind as we can possibly justify.

So how do we determine if a domain concept should be modeled as a Value? We have to pay close attention to its characteristics.

When you care only about the attributes of an element of the model, classify it as a VALUE OBJECT. Make it express the meaning of the attributes it conveys and give it related functionality. Treat the VALUE OBJECT as immutable. Don’t give it any identity and avoid the design complexities necessary to maintain ENTITIES. [Evans, p. 99]

As easy as it may be to create a Value type, sometimes those inexperienced with DDD face confusion when trying to choose whether to model an Entity or a Value in a specific instance. The truth is that even experienced designers struggle with this from time to time. Along with showing you how to implement a Value, I hope to clear up some of the mystery around the sometimes confusing decision-making process.

## 6.1 VALUE CHARACTERISTICS 值对象的特征

As a first order of business, make certain when modeling a domain concept as a Value Object that you are addressing the Ubiquitous Language. Consider this to be an overarching principle and a characteristic that must be achieved. I imply this principle throughout the chapter.

When you are trying to decide whether a concept is a Value, you should determine whether it possesses most of these characteristics:

- It measures, quantifies, or describes a thing in the domain.
- It can be maintained as immutable.
- It models a conceptual whole by composing related attributes as an integral unit.
- It is completely replaceable when the measurement or description changes.
- It can be compared with others using Value equality.
- It supplies its collaborators with Side-Effect-Free Behavior [Evans].

It will help to understand each of these characteristics in more detail. By employing this approach to analyzing design elements in the model, you may find that you should use Value Objects far more often than you may have before.

### 6.1.1 Measures, Quantifies, or Describes 度量或描述

When you have a true Value Object in your model, whether you realize it or not, it is not a thing in your domain. Instead, it is actually a concept that measures, quantifies, or otherwise describes a thing in the domain. A person has an age. The age is not really a thing but measures or quantifies the number of years the person (thing) has lived. A person has a name. The name is not a thing but describes what the person (thing) is called.

This is closely related to the Conceptual Whole characteristic.

### 6.1.2 Immutable 不变性

An object that is a Value is unchangeable after it has been created.1 When programming in Java or C#, for example, you use one of the Value class’s constructors to create an instance, passing in as parameters all objects on which its state will be based. The parameters may be the objects that will directly serve as the attributes of the Value, or they may be objects that will be used to derive one or more newly constituted attributes during construction. Here’s an example of a Value Object type that holds a reference to another Value Object:

1. There are times when a Value Object can be designed as mutable, but the need is usually rare. I don’t dwell on mutable Values here. If you are interested in when to use a mutable Value type, please see the sidebar on page 101 of [Evans].

```java
package com.saasovation.agilepm.domain.model.product;

public final class BusinessPriority implements Serializable {
    private BusinessPriorityRatings ratings;
    public BusinessPriority(BusinessPriorityRatings aRatings) {
        super();
        this.setRatings(aRatings);
        this.initialize();
    }
    ...
}
```

Instantiation alone does not guarantee that an object is immutable. After the object has been instantiated and initialized by means of construction, none of its methods, whether public or hidden, will from that time forward cause its state to change. In this example only the setRatings() and initialize() methods may mutate state because they are used only in the scope of construction. Method setRatings() is private/hidden and cannot be invoked from outside the instance.2 Further, class BusinessPriority must be implemented such that none of its methods other than constructors, public or hidden, may invoke the setter. Later I will discuss how to test Value Objects for immutability.

2. In some cases, frameworks such as object-relational mappers or serialization libraries (for XML, JSON, and so on) may need to use setters to reconstitute Value state from its serialized form.

Depending on your taste, you can at times design Value Objects that hold references to Entities. But some caution may be warranted. When the referenced Entities change state—by the Entity’s behavior—the Value changes, too, which violates the quality of immutability. Thus, it may be best to employ the mindset that Entity references held by Value types are used for the sake of compositional immutability, expressiveness, and convenience. Otherwise, if Entities are held with the express purpose of mutating their state through the Value Object’s interface, that’s probably the wrong reason to compose them. Weigh the competing forces while considering the Side-Effect-Free Behavior characteristic discussed later in the chapter.

::: tip
Challenge Your Assumptions

If you think that the object you are designing must be mutated by its behavior, ask yourself why that is necessary. Would it be possible instead to use replacement when the Value must change? Using this approach where possible is designing toward simplification.

Sometimes it makes no sense for an object to be immutable. That’s perfectly fine, and it indicates that the object should be modeled as an Entity. If your analysis leads you to that conclusion, refer to Entities (5).
:::

### 6.1.3 Conceptual Whole 概念整体

A Value Object may possess just one, a few, or a number of individual attributes, each of which is related to the others. Each attribute contributes an important part to a whole that collectively the attributes describe. Taken apart from the others, each of the attributes fails to provide a cohesive meaning. Only together do all the attributes form the complete intended measure or description. This is different from merely grouping a set of attributes together in an object. The grouping itself accomplishes little if the whole fails to adequately describe another thing in the model.

As Ward Cunningham illustrates in his Whole Value pattern3 [Cunningham, Whole Value aka Value Object], the Value {50,000,000 dollars} has two attributes: the attribute 50,000,000 and the attribute dollars. Separately these attributes describe something else or nothing special. This is especially true of the number 50,000,000, but certainly also of dollars. Together these attributes are a conceptual whole that describes a monetary measure. So we would not expect the thing that is said to be worth 50,000,000 dollars to have two separate attributes to describe its worth, one of amount that is 50,000,000 and one of currency that is dollars. Because the thing’s worth is not just 50,000,000, and not just dollars. Here’s the inexplicit way to model it:

3. Also called Meaningful Whole.

```java
// incorrectly modeled thing of worth
public class ThingOfWorth {
    private String name;         // attribute
    private BigDecimal amount;   // attribute
    private String currency;     // attribute
    // ...
}
```

In this example the model and its clients have to know when and how to use amount and currency together because they don’t form a conceptual whole. This begs for a better approach.

To properly describe a thing’s worth it must be treated not as two separate attributes, but as a whole value: {50,000,000 dollars}. Here it is modeled as a Whole Value:

```java
public final class MonetaryValue implements Serializable {
    private BigDecimal amount;
    private String currency;
    public MonetaryValue(BigDecimal anAmount, String aCurrency) {
        this.setAmount(anAmount);
        this.setCurrency(aCurrency);
    }
    ...
}
```

This is not to say that MonetaryValue is perfect and could not be improved. For sure, the use of an additional Value type such as Currency would help here. We’d replace the String type of the currency attribute with the much more descriptive Currency type. There may also be a good argument to have a Factory and possibly a Builder [Gamma et al.] to take care of that. But those topics would detract from the simple example that is meant to focus on the concept of Whole Value.

Because the wholeness of a concept in the domain is so important, the parent reference to a Value Object is not just an attribute. Rather, it is a property of the containing parent object/thing in the model that holds a reference to it. Granted, the type of the Value Object has one or more attributes (two in the case of MonetaryValue). But to the thing that holds the reference to the Value Object instance, it is a property. Therefore, the thing that is worth 50,000,000 dollars—let’s call it ThingOfWorth—would have a property—possibly named worth—that holds a reference to an instance of a Value Object that has two attributes that collectively describe the measure {50,000,000 dollars}. Remember, though, that the property name—possibly worth—and the Value type name—possibly MonetaryValue—can be determined only after establishing our Bounded Context (2) and its Ubiquitous Language. Here’s an improved implementation:

```java
// correctly modeled thing of worth
public class ThingOfWorth {
    private ThingName name;      // property
    private MonetaryValue worth; // property
    // ...
}
```

As expected, I changed ThingOfWorth to possess a property of type MonetaryValue that is named worth. It sure cleans up the otherwise disorganized attributes. But more importantly, there is now a Value that expresses a whole.

I want to draw attention to a second change, perhaps one that you were not expecting. The name of the ThingOfWorth may be just as important to aptly describe as is its worth. So I also replaced the String type of name with the ThingName type. The use of a String attribute for name could seem thorough enough at first. But, in later iterations, you learn that the use of a plain String causes problems. It has allowed domain logic central to the name of a ThingOfWorth to leak out of the model. It has leaked into other parts of the model and into client code:

```java
// clients deal with naming issues
String name = thingOfWorth.name();
String capitalizedName =
        name.substring(0, 1).toUpperCase()
        + name.substring(1).toLowerCase();
```

Here the client makes a feeble attempt to fix possible capitalization issues with the name. By defining a ThingName type instead, we can centralize all concerns dealing with the name of a ThingOfWorth. Based on this example, the ThingName may fully format the text name upon instantiation, relieving clients of that burden. This emphasizes the need to proliferate Values throughout the model as opposed to minimizing their significance and use. Now, rather than containing three less meaningful attributes, ThingOfWorth contains two properly typed and named property Values.

The constructors of a Value class play into the effectiveness of a conceptual whole. Along with immutability, we require a Value class’s constructors to be the means to ensure that the Whole Value is created in one operation. You must not allow the attributes of a Value instance to be populated after construction, as if to build up the Whole Value piece by piece. Instead, the final state must be guaranteed to initialize all at once, atomically. The previously expressed BusinessPriority and MonetaryValue constructors demonstrate this.

Here’s another angle on basic value type (for example, String, Integer, or Double) overuse. There are programming languages (such as Ruby) that allow you to effectively patch a class with new, specialized behavior. With such capabilities, you may consider using, for example, a double floating-point value to represent currency. If you need to calculate exchange rates between currencies, you could just patch class Double with a convertToCurrency(Currency aCurrency) behavior. This might seem like programming coolness, but is it really a good idea to use a language feature in this case? For one thing, this currency-specific behavior is probably lost in a sea of general-purpose floating-point responsibilities. Strike one. Likewise, there is no built-in understanding of currencies in class Double. So you’d have to build up the language default type to understand more about currencies. After all, you have to pass in a Currency to know the one to convert to. Strike two. Most importantly, class Double says nothing explicit about your domain. You lose track of your domain concerns by not applying the Ubiquitous Language. Big swing and a miss. Strike three.

::: tip
Challenge Your Assumptions

If you are tempted to place multiple attributes on an Entity that as a result manifests a weakened relationship to all other attributes, the attributes should very likely be gathered into a single Value type, or multiple Value types. Each should form a conceptual whole that reflects cohesiveness, appropriately named from your Ubiquitous Language. If even one attribute is associated with a descriptive concept, it is very possible that centralizing all concerns of this concept will improve the power of the model. If one or more of the attributes must change over time, consider Whole Value replacement over maintaining an Entity through a long life cycle.
:::

### 6.1.4 Replaceability 可替换性

In your model an immutable Value should be held as a reference by an Entity as long as its constant state describes the currently correct Whole Value. If that is no longer true, the entire Value is completely replaced with a new Value that does represent the currently correct whole.

The concept of replaceability is readily understood in the context of numbers. Say that you have the concept of a total that is an integer in your domain. If the total is currently set to the value 3 but must now be the value 4, you don’t, of course, modify the integer 3 itself to become the number 4. Instead you simply set the total to the integer 4:

```java
int total = 3;
// later...
total = 4;
```

This is obvious, but it helps make a point. In this example we have just replaced the total value 3 with the value 4. This is not an oversimplification. It is exactly what replacement does even when a given Value Object type is more complex than an integer. Consider a more complex Value type:

```java
FullName name = new FullName("Vaughn", "Vernon");
// later...
name = new FullName("Vaughn", "L", "Vernon");
```

The name starts out as the descriptive value of my first name and my last name. Later that Whole Value is replaced with the Whole Value of my first name, the first initial of my middle name, and my last name. I did not use a method on FullName to change the state of the value of name to contain the first initial of my middle name. That would violate the immutability quality of the FullName Value type. Rather we simply use Whole Value replacement, assigning the name object reference an entirely new instance of FullName. (True, this example was not an expressive way to handle replacement, and a better way is just ahead.)

::: tip
Challenge Your Assumptions

If you are leaning toward the creation of an Entity because the attributes of the object must change, challenge your assumptions that it’s the correct model. Would object replacement work instead? Considering the preceding replacement example, you may think that creating a new instance is impractical and lacks expressiveness. Even if the object you are dealing with is complex and changes somewhat frequently, replacement need not be an impractical, or even ugly, proposition. A later example demonstrates Side-Effect-Free Behavior for a simple and expressive way to deal with Whole Value replacement.
:::

### 6.1.5 Value Equality 值对象相等性

When a Value Object instance is compared to another instance, a test of object equality is employed. Throughout the system there may be many, many Value instances that are equal, and yet not the same objects. Equality is determined by comparing the types of both objects and then their attributes. If both the types and their attributes are equal, the Values are considered equal. Further, if any two or more Value instances are equal, you could assign (using replacement) any one of the equal Value instances to an Entity’s property of that type and the assignment would not change the value of the property.

Here’s an example of class FullName implementing a test for Value equality:

```java
public boolean equals(Object anObject) {
    boolean equalObjects = false;
    if (anObject != null &&
            this.getClass() == anObject.getClass()) {
        FullName typedObject = (FullName) anObject;
        equalObjects =
            this.firstName().equals(typedObject.firstName()) &&
            this.lastName().equals(typedObject.lastName());
    }
    return equalObjects;
}
```

Each of the attributes of two FullName instances is compared to the others (assuming this version has only first and last names, not a middle name). If all of the attributes in both objects are equal, the two FullName instances are considered equal. This particular Value prevents null firstName and lastName upon construction. Thus, there is no need to protect against null in equals() comparisons of each of the corresponding properties. Further, I favor the use of self-encapsulation, so I access attributes through their query methods. This allows for derived attributes rather than requiring each attribute to exist as explicit state. Also implied is the need for a corresponding hashCode() implementation (demonstrated later).

Consider the combination of Value characteristics necessary to support Aggregate (10) unique identity. We need the Value equality capability, for example, when we query for a specific Aggregate instance by identity. Immutability is also crucial. The unique identity must never change, and this can in part be ensured through the Value immutability characteristic. We also benefit from the conceptual whole characteristic, because the identity is named per the Ubiquitous Language and holds all uniqueness-identifying attributes in one instance. However, in this specific case we don’t need the replacement characteristic of a Value Object because the unique identity of an Aggregate Root will never be replaced. Yet, lacking the need for replacement characteristics does not disqualify the use of a Value here. Further, if the identity requires some Side-Effect-Free Behavior, it is implemented on the Value type.

::: tip
Challenge Your Assumptions

Ask yourself if the concept you are designing must be an Entity identified uniquely from all other objects or if it is sufficiently supported using Value equality. If the concept itself doesn’t require unique identity, model it as a Value Object.
:::

### 6.1.6 Side-Effect-Free Behavior 无副作用行为

A method of an object can be designed as a Side-Effect-Free Function [Evans]. A function is an operation of an object that produces output but without modifying its own state. Since no modification occurs when executing a specific operation, that operation is said to be side-effect free. The methods of an immutable Value Object must all be Side-Effect-Free Functions because they must not violate its immutability quality. You may consider this characteristic as part and parcel with immutability. It is closely tied. However, I prefer to break it out as a distinct characteristic because doing so emphasizes a huge benefit of Value Objects. Otherwise, we might see Values only as attribute containers, overlooking one of the most powerful aspects of the pattern.

::: tip
The Functional Way

Functional programming languages generally enforce this characteristic. In fact, pure functional languages allow nothing but Side-Effect-Free Behavior, requiring all closures to receive and produce only immutable Value Objects.
:::

Bertrand Meyer described Side-Effect-Free Functions as the Query methods of his Command-Query Separation principle, or CQS, as discussed by Martin Fowler in [Fowler, CQS]. A query method is one that asks an object a question. By definition, asking an object a question must not change the answer.

Here is an example of the FullName type’s use of Side-Effect-Free Behavior to produce a new replacement value of itself:

```java
FullName name = new FullName("Vaughn", "Vernon");

// later...

name = name.withMiddleInitial("L");
```

This produces the same outcome as the example discussed under “Replaceability,” but in a more expressive way. This Side-Effect-Free Function is implemented as follows:

```java
public FullName withMiddleInitial(String aMiddleNameOrInitial) {
    if (aMiddleNameOrInitial == null) {
        throw new IllegalArgumentException(
                "Must provide a middle name or initial.");
    }
    String middle = aMiddleNameOrInitial.trim();
    if (middle.isEmpty()) {
        throw new IllegalArgumentException(
                "Must provide a middle name or initial.");
    }
    return new FullName(
            this.firstName(),
            middle.substring(0, 1).toUpperCase(),
            this.lastName());
}
```

In this example the method withMiddleInitial() does not modify the state of its own Value and is, therefore, side-effect free. Instead it instantiates a new Value composed from some of its own parts and a given middle initial. This method captures important domain business logic in the model rather than allowing it to leak out into client code, which could happen in the earlier example.

::: tip
When a Value References an Entity

Should a Value Object method be permitted to cause the modification of an Entity that is passed as a parameter? Without stating a rule, if such a method does cause the modification of an Entity, is it really side-effect free? Would it be easy to test that method? I say not easy or less easy. Thus, when a Value’s method takes an Entity as parameter, it may be best for it to answer a result that the Entity could use to modify itself on its own terms.
:::

Nonetheless, there are problems with such a design. Consider an example. Here a Scrum Product, an Entity, is used in some way by BusinessPriority, a Value Object, to calculate a priority:

float priority = businessPriority.priorityOf(product);

Do you see flaws in this? You have probably concluded that at least some problems exist:

- What I draw attention to is that we are forcing the Value to not only depend on a Product, but also to understand the shape of this Entity. Where possible, limit a Value to depend on and understand only its own type and the types of its attributes. That is not always possible, but it’s a good goal.
- Someone reading the code will not know what parts of the Product will be used. The expression is not explicit, which weakens the clarity of the model. It would be much better if some actual or derived property of Product were passed.
- More important for this discussion, any Value method that takes an Entity as parameter cannot easily prove that it doesn’t cause the Entity’s modification, making the operation more difficult to test. So, even though a Value promises not to cause modification, no one can easily prove that it doesn’t.

Given this analysis, we haven’t really improved anything here. To change that and make the Value robust, you’d pass only Values as parameters to Value methods. This way you reach the greatest level of Side-Effect-Free Behavior. It is not difficult to accomplish:

```java
float priority =
        businessPriority.priority(
               product.businessPriorityTotals());
```

Here we simply ask the Product to provide an instance of Value BusinessPriorityTotals. You may conclude that priority() should return a type other than float. That would be especially true if expressing a priority should be a more formal part of the Ubiquitous Language, in which case a custom value type would be in order. Decisions like these come as a result of continually refining the model. Indeed, after some analysis the SaaSOvation team finds that the Product Entity should not itself calculate business priority totals at all. That would eventually be performed by a Domain Service (7), and you will see the better solution in that chapter.

If you decide against designing a specialized Value Object in favor of using a basic language Value type instead (primitive or wrapper), you might be shortchanging your model. You won’t have the opportunity to assign domain-specific Side-Effect-Free Functions to the basic language Value type. Any specialty behavior will be separate from the Value. And even if your programming language allows you to patch the basic type with new behavior, is that really going to enable you to capture deep domain insight?

::: tip
Challenge Your Assumptions

If you think that a specific method cannot be side-effect free and must mutate the state of its own instance, challenge your assumptions. Is there a way to employ replacement rather than mutation? The preceding example provides a very simple approach to creating a new Value by reusing parts of the existing one and replacing only the specifically changed parts. Rarely would every object in the system be a Value. Some objects will almost certainly be Entities. Carefully compare the Value characteristic qualifiers against those of Entities. A reasonable amount of team thought and discussion should lead to the correct conclusions.
:::

::: tip
Once the SaaSOvation teams read the [Evans] guidance about Side-Effect-Free Functions, and other Whole Value material, they realized that they should be using Value Objects far more frequently. The teams have since come to realize that understanding the preceding Value characteristics really helped them discover more natural Value types in their domain.
:::

::: tip
Is Everything a Value Object?

By now you may have begun to think that everything looks like a Value Object. That’s better than thinking that everything looks like an Entity. Where you might use a little caution is when there are truly simple attributes that really don’t need any special treatment. Perhaps those are Booleans or any numeric value that is really self-contained, needing no additional functional support, and is related to no other attributes in the same Entity. On their own the simple attributes are a Meaningful Whole. Still, you could certainly make the “mistake” of unnecessarily wrapping a single attribute in a Value type with no special functionality and be better off than those who never give Value design a nod. If you find that you’ve overdone it a bit, you can always refactor a little.
:::

## 6.2 INTEGRATE WITH MINIMALISM 最小化集成

There are always multiple Bounded Contexts in every DDD initiative, which means we must find appropriate ways to integrate them. Where possible use Value Objects to model concepts in the downstream Context when objects from the upstream Context flow in. By doing so you can integrate with a priority on minimalism, that is, minimizing the number of properties that you assume responsibility for managing in your downstream model. Using immutable Values results in assuming less responsibility.

::: tip
Why Be So Responsible?

Using immutable Values results in assuming less responsibility.
:::

Reusing an example from Bounded Contexts (2), recall that two Aggregates in the upstream Identity and Access Context have an impact on the downstream Collaboration Context, as illustrated in Figure 6.1. In the Identity and Access Context the two Aggregates are User and Role. The Collaboration Context is interested in whether a specific User plays a specific Role, namely, Moderator. The Collaboration Context uses its Anticorruption Layer (3) to query the Open Host Service (3) of the Identity and Access Context. If the integration-based query indicates that the Moderator role is being played by the specific user, the Collaboration Context creates a representative object, namely, a Moderator.

<Figures figure="6-1">The Moderator object in its Context is based on the state of a User and Role in a different Context. User and Role are Aggregates, but Moderator is a &lt;&lt;value object&gt;&gt;</Figures>

Moderator, among the Collaborator subclasses shown in Figure 6.2, is modeled as a Value Object. Instances are statically created and associated with a Forum Aggregate, the important point being the minimized impact that multiple Aggregates in the upstream Identity and Access Context, possessing many attributes, have on the Collaboration Context. With just a few attributes of its own, a Moderator models an essential concept of the Ubiquitous Language spoken in the Collaboration Context. Furthermore, class Moderator contains no single attribute from the Role Aggregate. Rather, the class name itself captures the Moderator role played by a user. By choice, the Moderator is a statically created Value instance, and there is no goal to keep it synchronized with the remote Context of origin. This carefully chosen quality-of-service contract lifts a potentially heavy burden off the consuming Context.

<Figures figure="6-2">The Collaborator class hierarchy of Value Objects. Only a few User attributes are retained from the upstream Context, with class names making roles explicit.</Figures>

Of course, there are times when an object in a downstream Context must be eventually consistent with the partial state of one or more Aggregates in a remote Context. In that case we’d design an Aggregate in the downstream consuming Context, because Entities are used to maintain a thread of continuity of change. But we should strive to avoid this modeling choice where possible. When you can, choose Value Objects to model integrations. This advice is applicable in many cases when consuming remote Standard Types.

## 6.3 STANDARD TYPES EXPRESSED AS VALUES 用值对象表示标准类型

In many systems and applications there is a need for what I call Standard Types. Standard Types are descriptive objects that indicate the types of things. There is the thing (Entity) or description (Value) itself, and there are also the Standard Types to distinguish them from other types of the same thing. I am unaware of an industry standard name for this concept, but I have also heard it called a type code and a lookup. The name type code doesn’t say much. And a lookup is a lookup of what? I prefer the name Standard Types because it is more descriptive. To make this concept clearer, consider a few uses. In some cases these are modeled as Power Types.

Your Ubiquitous Language defines a PhoneNumber (Value), which also requires you to describe the type of each one. “Is the phone number a home, mobile, work, or other type of number?” asks your domain expert. Should different types of phone numbers be modeled as a class hierarchy? Having a separate class for each type makes it more difficult for clients to distinguish among them. Here you’d likely desire to use a Standard Type to describe the type of phone, either Home, Mobile, Work, or Other. These descriptions represent the Standard Types of phones.

As I previously discussed, in a financial domain there is the possibility of having a Currency (Value) type to constrain a MonetaryValue to an amount within a specific world currency. In this case the Standard Type would provide a Value for each of the world’s currencies: AUD, CAD, CNY, EUR, GBP, JPY, USD, and so on. Using a Standard Type here helps you avoid bogus currencies. Although the incorrect currency could be assigned to the MonetaryValue, a nonexistent currency could not be. If using a string attribute, you could place the model into an invalid state. Consider the misspelled doolars and the problems it would cause.

You might be working in the pharmaceutical field and designing for medications that have various kinds of administration routes. A specific medication (Entity) has a long life cycle and change is managed over time—it is conceptualized, researched, developed, tested, manufactured, improved, and finally discontinued. You may or may not decide to manage the life cycle stages using Standard Types. These life cycle shifts may justifiably be managed in a few different Bounded Contexts. On the other hand, the directed patient administration route of each medication can be classified by Standard Type descriptions, such as IV, Oral, or Topical.

Depending on the level of standardization, these types may be maintained at the application level only, or be escalated in importance to shared corporate databases, or be available through national or international standards bodies.

The level of standardization can sometimes influence the way Standard Types are retrieved and used inside a model.

We may think of these as Entities because they have a life of their own in a dedicated, native Bounded Context. Regardless of how they are created and maintained by any kind of standards body, if possible we should strive to treat them as Values in our consuming Context. This works well because they measure and describe the types of things, and measures and descriptions are best modeled as Values. Further, one instance of {IV}, for example, is just the same as any other instance of {IV}. They are clearly interchangeable, which also means that they are replaceable and can employ Value equality. Thus, if there is no need to maintain a continuity of change over the life cycle of descriptive types in your Bounded Context, model them as Values.

For the sake of maintenance it is common for Standard Types to natively reside in a separate Context from the models that consume them. There they are Entities and have a persistent life cycle with attributes such as identity, name, and description. There may be other attributes as well, but the ones mentioned are the most common to use in a consuming Context. We often use just one. This is in adherence to the goal to integrate with minimalism.

As a very simple example, consider a Standard Type that models a member of a group for which two types exist. There may be members that are users and members that are themselves groups (nested groups). This Java enum represents one way to support a Standard Type:

```java
package com.saasovation.identityaccess.domain.model.identity;

public enum GroupMemberType {
    GROUP {
        public boolean isGroup() {
            return true;
        }
    },
    USER {
        public boolean isUser() {
            return true;
        }
    };
    public boolean isGroup() {
        return false;
    }
    public boolean isUser() {
        return false;
    }
}
```

A GroupMember Value instance is instantiated with a specific Group-MemberType. To demonstrate, when a User or a Group is assigned to a Group, the assigned Aggregate is asked to render a GroupMember corresponding to itself. Here is the toGroupMember() method implementation of class User:

```java
protected GroupMember toGroupMember() {
    GroupMember groupMember =
        new GroupMember(
                this.tenantId(),
                this.username(),
                GroupMemberType.USER); // enum standard type
    return groupMember;
}
```

Use of a Java enum is a very simple way to support a Standard Type. The enum provides a well-defined finite number of Values (in this case two), it is very lightweight, and it has by convention Side-Effect-Free Behavior. But where is the Value’s textual description? There are two possible answers. Often there is no need to provide a description of the type, just its name. Why? Textual descriptions are generally valid only in the User Interface Layer (14) and can be supplied by matching the type name to a view-centric property. Many times the view-centric property must be localized (as in multilanguage computing), making this inappropriate to support in the model. Often the name of the Standard Type alone is the best attribute to use in the model. The second answer is that there are limited descriptions built right into the enum state names GROUP and USER. You may render the descriptive names using the toString() behavior of each type. But if necessary, descriptive text of each type may be modeled as well.

This sample Java enum Standard Type is also, in essence, an elegant and clutter-free State [Gamma et al.] object. At the bottom of the enum declaration there are two methods that implement the default behavior for all States, isGroup() and isUser(). By default, both of these methods answer false, which is the proper basic behavior. In each of the State definitions, however, the methods are overridden to answer true as applicable for their specific State. When the state of the Standard Type is the GROUP, the isGroup() method is overridden to produce a true outcome. When the state of the Standard Type is the USER, the isUser() method is overridden to produce a true outcome. The state changes by replacing the current enum value with a different one.

This enum demonstrates some very basic behavior. The State pattern implementation can be more sophisticated as needed by the domain, adding more standard behaviors that are overridden and specialized by each State. As it is, this is an example of a Value type whose states are constrained to a well-defined set of constants. An important one is the BacklogItemStatusType, which provides PLANNED, SCHEDULED, COMMITTED, DONE, and REMOVED states. I use this Standard Type approach throughout the three sample Bounded Contexts. I think it keeps them as simple as possible.

::: tip
State Pattern Considered Harmful?

Some consider the State pattern to be less than desirable. A common complaint is the need to create an abstract implementation of each of the behaviors supported by the type (the two methods at the bottom of GroupMemberType) and then to override those behaviors when the given State must provide a specialized implementation. In Java this typically requires a separate class (usually in a separate file) for the abstract type and also for each State. Like it or not, that is the way of the State pattern.

I agree that when separate State classes must be developed—one for each unique state plus an abstract type—it can become an unwieldy mess. The distinct behaviors in each class, perhaps mixed with some default behavior from the abstract class, can lead to tight coupling of subclasses and lack of readability between types. This burden is especially taxing if you have a large number of States. However, I think that the use of a Java enum is a very simple and possibly the more optimal way to use the State pattern to produce a set of Standard Types. I think you get the best of both approaches. You get a very simple Standard Type and a way to interrogate the standard for its current State. This keeps behavior cohesive to the type. Limiting the State behavior makes for practical use.

But it’s still possible that you don’t like even this simple implementation of State, and to each his or her own.
:::

If you decide that you dislike the use of Java enums to support Standard Types, you can always use a unique Value instance for each type. However, if your concern is primarily that you don’t like the idea of using the State pattern, you can easily use an enum for elegant Standard Type support without thinking of it as the State pattern. After all, I may be the first person to have put the enum-as-State thought into your mind. That being said, there are alternatives to implementing Standard Types other than the enum and Value approaches.

As one alternative, you can use an Aggregate as a Standard Type with one instance of the Aggregate per type. Think twice before you run with this. Standard types should generally not be maintained inside the Bounded Context that consumes them. Widely used Standard Types should normally be maintained in a separate Context with very carefully planned updates to consumers. Instead, you could choose to expose Standard Type Aggregates as immutable in consumer Contexts. But ask yourself if an immutable Entity is by definition really an Entity. If you think not, you should consider modeling it as a shared immutable Value Object instead.

A shared immutable Value Object can be obtained from a hidden persistence store. This is a viable choice if obtained from a Standard Type Service (7) or Factory (11). If employed, you should probably have one Service or Factory provider for each set of Standard Types (one for phone number types, another for postal address types, one for currency types), as depicted in Figure 6.3. In both cases the concrete implementations of a Service or Factory would access the persistence store to obtain the shared Values as needed, but clients would never know that the Values are stored in a standards database. Using either a Service or a Factory to provide the types also enables you to put a number of viable caching strategies to work easily and safely because the Values are read-only from the store and immutable in the system.

<Figures figure="6-3">A Domain Service can be used to provide Standard Types. In this case the Service goes out to the database to read the state of a requested CurrencyType.</Figures>

In the end, I think it is best to be biased toward enum for Standard Types whether or not you actually think of it as a State. If you have many possible Standard Type instances in a single category, consider code generation to produce the enum. A code generation approach could read through all existing Standard Types in their respective persistence store (system of record) and create a unique type/state per row, for example.

If you decide to use classical Value Objects as Standard Types, you may find it useful to introduce a Service or Factory to statically create instances as needed. This would have similar motivations as discussed previously but would be different in its implementation from those producing shared Values. In this case your Service or Factory would provide statically created immutable Value instances of each individual Standard Type. Any changes to the underlying Standard Type database entities in the system of record would not be automatically reflected in the preexisting statically created representation instances. If you desired to keep such statically created Value instances synchronized with the system of record, you’d need to provide a custom solution to search for and update their state in your model. This could negate the potential usefulness of this approach.4 Thus, you might from design inception determine that all such statically created Standard Type Values will never be updated in the consuming Bounded Context. All competing forces must be weighed.

4. This would be a good time to model an Aggregate in an upstream Context also as an Aggregate in the downstream Context. They wouldn’t be the same class or necessarily contain all the same attributes, but modeling the downstream concept as an Aggregate would allow for eventual consistency and single point updates.

## 6.4 TESTING VALUE OBJECTS 测试值对象

To emphasize test-first, I first present sample tests before I provide the Value Object implementation. These tests drive the domain model’s design by providing examples of how a client will use each object.

Employing this style, we are not as interested in addressing the various aspects of unit testing, thoroughly proving that the model is completely bulletproof in every way. Rather, at this point in time there is more interest in demonstrating how various objects in the domain model will be used by clients and what those clients can expect when they use them. It is essential to assume the client’s perspective when designing the model in order to capture the essential concepts. Otherwise, we could be modeling from our own perspective instead of from the business’s.

::: tip
Best Sample Code

Here’s one way of thinking about this style of test: If we were writing a user’s manual for the model, we would provide these tests as the most appropriate code samples for how clients should use this specific domain object.
:::

This is not to say that unit tests should not be developed. All additional tests that address team standards should and must be written. However, there are different motivations for each type of test. Unit tests and behavioral tests have their place, as do the following modeling tests.

The Value Object selected is a good all-around representation taken from the latest Core Domain (2), the Agile Project Management Context.

::: tip
![](./figures/ch6/team_huddle.jpg)

In this Bounded Context business domain experts speak of the “business priority of backlog items.” To fulfill this part of the Ubiquitous Language we model the concept as a BusinessPriority. It provides calculated output suitable for supporting the business analysis of the value of developing each product backlog item [Wiegers]. The outputs are cost percentage, or the cost of developing a specific backlog item as compared to the cost of developing all others; total value, which is the total value gained by developing a specific backlog item; and value percentage, as in the value of developing a specific backlog item compared to the value of developing any other; and priority, which is the calculated priority the business should consider giving this backlog item when compared against all others.

These tests actually emerged over multiple brief refactoring iterations of stepwise refinements, although they are presented here as a finished set:

```java
package com.saasovation.agilepm.domain.model.product;

import com.saasovation.agilepm.domain.model.DomainTest;
import java.text.NumberFormat;

public class BusinessPriorityTest extends DomainTest {
    public BusinessPriorityTest() {
        super();
    }
    ...
    private NumberFormat oneDecimal() {
        return this.decimal(1);
    }
    private NumberFormat twoDecimals() {
        return this.decimal(2);
    }
    private NumberFormat decimal(int aNumberOfDecimals) {
        NumberFormat fmt = NumberFormat.getInstance();
        fmt.setMinimumFractionDigits(aNumberOfDecimals);
        fmt.setMaximumFractionDigits(aNumberOfDecimals);
        return fmt;
    }
}
```

The class has some fixture helpers. Since the team needed to test the accuracy of various calculations, they coded methods to provide NumberFormat instances for fractional values that had either one or two places to the right of the decimal point. You’ll see next why these are useful:

```java
    public void testCostPercentageCalculation() throws Exception {
        BusinessPriority businessPriority =
            new BusinessPriority(
                    new BusinessPriorityRatings(2, 4, 1, 1));
        BusinessPriority businessPriorityCopy =
            new BusinessPriority(businessPriority);
        assertEquals(businessPriority, businessPriorityCopy);
        BusinessPriorityTotals totals =
            new BusinessPriorityTotals(53, 49, 53 + 49, 37, 33);
        float cost = businessPriority.costPercentage(totals);
        assertEquals(this.oneDecimal().format(cost), "2.7");
        assertEquals(businessPriority, businessPriorityCopy);
    }
```

The team came up with a good idea to test for immutability. Each test first created an instance of BusinessPriority and then made an equivalent copy of it using the copy constructor. The first assertion in the test ensured that the copy constructor produced a copy equal to the original.

Next, they designed the test to create BusinessPriorityTotals and assigned it to the totals method variable. With totals they were able to use the cost-Percentage() query method and assign the results to cost. They then asserted that the value returned was 2.7, which was the manually calculated correct outcome. Finally, they asserted that the behavior of method costPercentage() was truly side-effect free, which would be the case if businessPriority still had Value equality with businessPriorityCopy. From this test they gained a good idea of how to calculate cost percentages and what their outcome would be like.

Next, they needed to test the priority, the total value, and the value percentage calculations, using the same basic plan of attack:

```java
    public void testPriorityCalculation() throws Exception {
        BusinessPriority businessPriority =
            new BusinessPriority(
                    new BusinessPriorityRatings(2, 4, 1, 1));
        BusinessPriority businessPriorityCopy =
            new BusinessPriority(businessPriority);
        assertEquals(businessPriorityCopy, businessPriority);
        BusinessPriorityTotals totals =
            new BusinessPriorityTotals(53, 49, 53 + 49, 37, 33);
        float calculatedPriority = businessPriority.priority(totals);
        assertEquals("1.03",
                     this.twoDecimals().format(calculatedPriority));
        assertEquals(businessPriority, businessPriorityCopy);
    }
    public void testTotalValueCalculation() throws Exception {
        BusinessPriority businessPriority =
            new BusinessPriority(
                    new BusinessPriorityRatings(2, 4, 1, 1));
        BusinessPriority businessPriorityCopy =
            new BusinessPriority(businessPriority);
        assertEquals(businessPriority, businessPriorityCopy);
        float totalValue = businessPriority.totalValue();
        assertEquals("6.0", this.oneDecimal().format(totalValue));
        assertEquals(businessPriority, businessPriorityCopy);
    }
    public void testValuePercentageCalculation() throws Exception {
        BusinessPriority businessPriority =
            new BusinessPriority(
                    new BusinessPriorityRatings(2, 4, 1, 1));
        BusinessPriority businessPriorityCopy =
            new BusinessPriority(businessPriority);
        assertEquals(businessPriority, businessPriorityCopy);
        BusinessPriorityTotals totals =
            new BusinessPriorityTotals(53, 49, 53 + 49, 37, 33);
        float valuePercentage =
               businessPriority.valuePercentage(totals);
        assertEquals("5.9", this.oneDecimal().format(valuePercentage));
        assertEquals(businessPriorityCopy, businessPriority);
    }
```

Tests Should Have Domain Meaning

Your model tests should have meaning to your domain experts.

Nontechnical domain experts—given a bit of help—reading these example-based tests were able to understand just how BusinessPriority was used, the kinds of outcomes it produced, that its behavior was guaranteed to be side-effect free, and that it adhered to the concepts and intent of the Ubiquitous Language.

Importantly, the state of the Value Object was guaranteed immutable for every usage. Clients could produce results from the priority calculations of any number of product backlog items, sort them, compare them, and adjust the BusinessPriorityRatings of each item as needed.
:::

## 6.5 IMPLEMENTATION 实现

I like this BusinessPriority example because it demonstrates all of the Value characteristics and more. Besides showing how to design for immutability, conceptual wholeness, replaceability, Value equality, and Side-Effect-Free Behavior, it also demonstrates how you can use a Value type as a Strategy [Gamma et al.] (aka Policy).

::: tip
![](./figures/ch6/team_huddle-c2.jpg)

As each test method was developed, the team understood more about how a client would use a BusinessPriority, enabling them to implement it to behave as the tests asserted it should. Here is the basic class definition along with constructors that the team coded:

```java
public final class BusinessPriority implements Serializable {
    private static final long serialVersionUID = 1L;
    private BusinessPriorityRatings ratings;
    public BusinessPriority(BusinessPriorityRatings aRatings) {
        super();
        this.setRatings(aRatings);
    }
    public BusinessPriority(BusinessPriority aBusinessPriority) {
        this(aBusinessPriority.ratings());
    }
}
```

The team decided to declare their Value types Serializable. There are times when a Value instance needs to be serialized, such as when it is communicated to a remote system, and may be useful for some persistence strategies.

This BusinessPriority itself was designed to hold a Value property named ratings of type BusinessPriorityRatings (not shown here). The ratings property described the business value and expense trade-off of either implementing, or not implementing, a given product backlog item. The BusinessPriority-Ratings type provided the BusinessPriority with benefit, cost, penalty, and risk ratings, which enabled a range of calculations to be performed.
:::

Usually I support at least two constructors for each of my Value Objects. The first constructor takes the full complement of parameters necessary to derive and/or set state attributes. This primary constructor first initializes its default state. The basic attribute initialization is performed first by invoking private setters. I recommend the use of self-delegation and demonstrate its use here with private setters.

::: tip
Keeping Values Immutable

Only the primary constructor(s) use self-delegation to set properties/attributes. No other methods shall self-delegate to setter methods. Since all setter methods in a Value Object are always private scope, there is no opportunity for attributes to be exposed to mutation by consumers. These are two important factors in maintaining the immutability of Values.
:::

The second constructor is used to copy an existing Value to create a new one, or what is called a copy constructor. This constructor performs what’s known as a shallow copy as it self-delegates to its primary constructor, passing as parameters each of the corresponding attributes of the Value being copied. We could perform a deep copy or clone where all contained attributes and properties are themselves copied to produce a completely unique object, but still equal to the value of the one copied. However, this many times proves to be both complex and unnecessary when dealing with Values. If a deep copy is ever needed, it can be added. But when dealing with immutable Values, it is never a problem to share attributes/properties between instances.

This second constructor, the copy constructor, is important for unit tests. When we test a Value Object, we want to include verification that it is immutable. As demonstrated earlier, when the unit test begins, create the new test Value Object instance and a copy of it using the copy constructor, and assert that the two instances are equal. Next, test the Value instance Side-Effect-Free Behavior. If all test goal assertions pass, the final assertion is that the tested and the copied instances are still equal.

Next, we implement the Strategy/Policy part of the Value type:

```java
public float costPercentage(BusinessPriorityTotals aTotals) {
    return (float) 100 * this.ratings().cost() /
        aTotals.totalCost();
}
public float priority(BusinessPriorityTotals aTotals) {
    return
        this.valuePercentage(aTotals) /
            (this.costPercentage(aTotals) +
                this.riskPercentage(aTotals));
}
public float riskPercentage(BusinessPriorityTotals aTotals) {
    return (float) 100 * this.ratings().risk() /
        aTotals.totalRisk();
}
public float totalValue() {
    return this.ratings().benefit() + this.ratings().penalty();
}
public float valuePercentage(BusinessPriorityTotals aTotals) {
    return (float) 100 * this.totalValue() / aTotals.totalValue();
}
public BusinessPriorityRatings ratings() {
    return this.ratings;
}
```

Some of the calculation behavior requires a parameter of type BusinessPriorityTotals. This Value provides a description of the cost-risk totals across all product backlog items. Totals are necessary when calculating percentages and the overall business priority compared to all other backlog items. None of these behaviors modifies its own instance state. We assert this externally in tests by comparing the copied state with the current state following the execution of each behavior.

There currently is no Separated Interface [Fowler, P of EAA] for the Strategy because there is at present only one implementation. No doubt in time that will change, and customers of the Agile PM SaaS product will be given other business priority calculation options, each with its own Strategy implementation.

The method names of the Side-Effect-Free Functions are important. Although these methods all return Values (because they are CQS query methods), they purposely avoid the use of the get-prefix JavaBean naming convention. This simple but effective approach to object design keeps the Value Object faithful to the Ubiquitous Language. The use of getValuePercentage() is a technical computer statement, but valuePercentage() is a fluent human-readable language expression.

::: tip
Where Did My Fluent Java Go?

I think that the JavaBean specification has had a negative impact on object design, one that doesn’t promote the principles of Domain-Driven Design or good object design in general. Consider the Java API that existed prior to the JavaBean specification. Take java.lang.String, for one example. There are but a few query methods on the class String that are prefixed by get. Most of the query methods are named more fluently, such as charAt(), compareTo(), concat(), contains(), endsWith(), indexOf(), length(), replace(), startsWith(), substring(), and the like. There’s no Java Bean code smell there! Of course, this example alone doesn’t prove my point. Nonetheless, it is true that Java APIs since the JavaBean specification have been greatly influenced and lack fluency in expression. A fluent, human-readable language expression is a very worthwhile style to embrace.

If you are concerned about tooling that depends on the JavaBean specification, there are solutions. For example, Hibernate provides support for field-level access (object attributes). Thus, as far as Hibernate is concerned, your methods can be named as desired without a negative impact on persistence.
:::

With other tools there could be a downside to designing with expressive interfaces, however. If you desire to use the standard Java EL or OGNL, for instance, you won’t be able to render such types directly. You would have to use another means, such as a Data Transfer Object [Fowler, P of EAA] with getters, to transfer Value Object properties to the user interface. Since DTO is a commonly used pattern, albeit often technically unnecessary, some may find this of little consequence. If DTO is not an option for you, there are others. Consider the Presentation Model as discussed in Application (14). Since your Presentation Model can serve as an Adapter [Gamma et al.], it can surface getters for use by views that use EL, for example. Yet, if all else fails, you may need to grudgingly design your domain objects with getters.

If you reach that conclusion, you should still not design Value Objects with full JavaBean capabilities that would allow their state to be initialized through public setters. That would violate their essential Value immutability characteristic.

The next set of methods includes the standard object overrides equals(), hashCode(), and toString():

```java
@Override
public boolean equals(Object anObject) {
    boolean equalObjects = false;
    if (anObject != null &&
            this.getClass() == anObject.getClass()) {
        BusinessPriority typedObject = (BusinessPriority) anObject;
        equalObjects =
            this.ratings().equals(typedObject.ratings());
    }
    return equalObjects;
}
@Override
public int hashCode() {
    int hashCodeValue =
        + (169065 * 179)
        + this.ratings().hashCode();
    return hashCodeValue;
}
@Override
public String toString() {
    return
        "BusinessPriority"
        + " ratings = " + this.ratings();
}
```

The equals() method fulfills the Value Object requirement to check for Value equality, one of the five Value characteristics. Here we always eliminate null parameters from equality. The class of the parameter must be the same as the class of the Value. If they are the same, each of the properties/attributes is compared in both Values. If each one is affirmed as equal to its corresponding property/attribute, the Whole Values are considered equal.

Per Java standards, hashCode() has the same contract as equals() in that all Values that are equal also produce equal hash code values.

There is nothing special about toString(). It creates a human-readable representation of the Value instance state. You may design the representation format as needed.

There are a few remaining methods to review:

```java
    protected BusinessPriority() {
        super();
    }
    private void setRatings(BusinessPriorityRatings aRatings) {
        if (aRatings == null) {
            throw new IllegalArgumentException(
                    "The ratings are required.");
        }
        this.ratings = aRatings;
    }
}
```

The zero-argument constructor is provided for the sake of framework tools that require it, such as Hibernate. Since the zero-argument constructor is always hidden, there is no danger of model clients creating invalid instances. Hibernate functions perfectly well with hidden constructors and accessors. This constructor enables Hibernate and other tools to create instances of the type as they are being reconstituted from, for example, the persistence store. Tools use the zero-argument constructor to create an initially hollow instance and then call each property/attribute setter to hydrate the object. Optionally you can tell Hibernate to bypass setter methods and directly set attributes, as is the case with this model since it doesn’t provide a complete JavaBean interface. Just to reiterate, model clients use the public constructors, never the hidden one.

Finally, the class definition ends with the property setter for ratings. One of the strengths of self-encapsulation/delegation is seen in this method. An accessor method—getter or setter—need not be limited to setting an instance field. It can also perform important Assertions [Evans], a key element to successful software development in general and DDD models specifically.

The Assertion for valid parameters is called a guard, because it guards the method from being subjected to obviously invalid data. Guards can and should be used in any method when wrong parameters would cause more serious problems later if correctness were otherwise taken for granted. Here the setter asserts that the parameter aRatings is not null, and if it happens to be, it throws an IllegalArgumentException. True, the setter is logically used only once in the Value’s lifetime. Still, the Assertion is a well-placed guard. You will see the advantages of self-delegation demonstrated elsewhere, too. Specifically, Entities (5) explains the technique thoroughly as part of a discussion of validation.

## 6.6 PERSISTING VALUE OBJECTS 持久化值对象

There are a variety of ways to persist Value Object instances to a persistent store. In a general sense it involves serializing the object to some text or binary format and saving it to disk. However, since we are not concerned with persisting individual Value instances on their own, I won’t be focusing on general-purpose persistence. Rather, we are more interested in persisting Values along with the state of the Aggregate instances that contain them. The following approaches assume that a parent Entity ultimately holds references to the Value instances that get persisted. All of the following examples are based on the assumption that an Aggregate is being added to or read from its Repository (12), and its contained Values are persisted and reconstituted behind the scenes along with the Entity—such as the Aggregate Root—that contains them.

Object-relational mapping (ORM, such as Hibernate) persistence is popular and widely used. However, using an ORM to map every class to a table and every attribute to a column adds complexity, which may be unwarranted. Rising in popularity is the use of NoSQL databases and key-value stores because of their ability to provide high-performance, scalable, fault-tolerant, and highly available enterprise storage. To boot, key-value stores can greatly simplify Aggregate persistence. In this chapter I stick with ORM-based persistence. Because NoSQL, key-value stores persist Aggregates exceptionally well, I give attention to that style in Repositories (12).

But before we jump into Value ORM persistence examples, there’s a vital modeling commitment that must be well understood and diligently followed. So to start off, let’s tackle what can happen when data modeling (as opposed to domain modeling) has an inappropriate influence on your domain model, and what can be done to reject this wrong and harmful influence.

### 6.6.1 Reject Undue Influence of Data Model Leakage 拒绝由数据建模泄露带来的不利影响

Probably most times that a Value Object is persisted to a data store (for example, using an ORM tool along with a relational database) it is stored in a denormalized fashion; that is, its attributes are stored in the same database table row as its parent Entity object. This makes the storage and retrieval of Values clean and optimized and prevents any persistence store leakage into the model. It’s both a pleasure and a relief when Values can be persisted this way.

There are times, however, when a Value Object in the model will of necessity be stored as an Entity with respect to a relational persistence store. In other words, when persisted, an instance of a specific Value Object type will occupy its own row in a relational database table that exists specifically for its type, and it will have its own database primary key column. This happens, for example, when supporting a collection of Value Object instances with ORM. In such cases the Value type persistent data is modeled as a database entity.

Is this an indication that the domain model object should reflect the design of the data model and be an Entity rather than a Value? No. When you face the consequences of this impedance mismatch, it is important to maintain a domain model perspective rather than a persistence perspective. To keep your perspective on the domain model you can ask yourself these questions:

1. Is the concept I am modeling a thing in the domain or does it measure, quantify, or describe a thing as one of its properties?
2. If modeled correctly to describe an element of the domain, must this model concept possess all or most of the value characteristics outlined previously?
3. Am I considering the use of an Entity in the model only because the underlying data model must store the domain model object as an entity?
4. Am I using an Entity because the domain model requires unique identity, I care about individual instances, and I must manage a continuity of change over the object’s life cycle?

If your answers are “Describes, Yes, Yes, and No,” you should use a Value Object. Model the persistence store in the way necessary to deal with the storage of the object, but don’t let that influence the way your team conceptualizes the Value property in the domain model.

::: tip
The Data Model Should Be Subordinate

Design your data model for the sake of your domain model, not your domain model for the sake of your data model.
:::

If at all possible, always design your data model for the sake of your domain model, not your domain model for the sake of your data model. If you do the former, you will maintain a domain model perspective. If you do the latter, you will maintain a persistence perspective and your domain model will tend to serve merely as a projection of your data model. As you discipline your mind to think in terms of the domain model—DDD-think—rather than the data model, you will avoid the negative consequences of data model leakage. See Entities (5) for more discussion of DDD-think.

Of course, there are times when database referential integrity matters (such as for foreign keys). Absolutely, you want key columns to be properly indexed. Sure, there certainly is the need to support business intelligence reporting tools that operate against your business data. You can enable all these facets in appropriate and necessary places. Most conclude that reporting and business intelligence should not operate against your production data and should instead have a dedicated, specially designed data model. Following this more strategic mentality frees you to design your domain model’s backing data model to best support your DDD efforts.

Whatever technical facets your data model uses, its entities, primary keys, referential integrity, and indexes simply must not drive the way you model domain objects. DDD is not about structuring data in a normalized fashion. It is about modeling the Ubiquitous Language in a consistent Bounded Context. I encourage you to adhere to DDD, not to data structure. As you do so, you should wisely take every possible step to hide all vestiges of data model leakage (which will occur to at least a minimal degree when using an ORM) from your domain model and its clients. This is something I discuss in the next section.

### 6.6.2 ORM and Single Value Objects ORM 与单个值对象

Persisting a single Value Object instance to a database is usually very straightforward. Here my focus is on the use of Hibernate with the MySQL relational database. The basic idea is to store each of the attributes of the Value in separate columns of the row where its parent Entity is stored. Said another way, a single Value Object is denormalized into its parent Entity’s row. There are advantages to employing convention for column naming to clearly identify and standardize the way serialized objects are named. I present a persisted Value Object naming convention here.

When using Hibernate to persist a single instance of a Value Object, use the component mapping element. The component element is employed because it enables the Value to be mapped directly into the parent Entity table row in a denormalized fashion. This is an optimal serialization technique that still enables Values to be included in SQL queries. Here is the section of the Hibernate mapping document that describes the mapping of the Business-Priority Value Object held by its parent Entity, class BacklogItem:

```xml
<component name="businessPriority" class="com.saasovation.agilepm.domain.model.product.BusinessPriority">
    <component name="ratings" class="com.saasovation.agilepm.domain.model.product.BusinessPriorityRatings">
        <property name="benefit" column="business_priority_ratings_benefit" type="int" update="true" insert="true" lazy="false" />
        <property name="cost" column="business_priority_ratings_cost" type="int" update="true" insert="true" lazy="false" />
        <property name="penalty" column="business_priority_ratings_penalty" type="int" update="true" insert="true" lazy="false" />
        <property name="risk" column="business_priority_ratings_risk" type="int" update="true" insert="true" lazy="false" />
    </component>
</component>
```

This is a good example because it demonstrates a simple Value Object mapping, but one that contains a child Value Object instance. Recall that BusinessPriority has a single ratings Value property and no additional attributes. Thus, in the mapping description the outer component element has a nested component element. This is used to denormalize the single contained ratings Value property of type BusinessPriorityRatings. Since the BusinessPriority has no attributes of its own, there are none mapped in the outer component. Instead we immediately nest the mapping of its ratings Value property. In the end, we actually store only the four integer attributes of the BusinessPriorityRatings instance into four separate columns of table tbl_backlog_item. So we map two component element Value Objects, one that has no attributes of its own and an inner Value that has four attributes.

Note the use of standard column naming of each of the Hibernate property elements. The naming convention is based on the navigation path from the ultimate parent Value down to the individual attributes. For example, consider the navigation path from the BusinessPriority down to the benefit attribute of the ValueCostRiskRatings instance. Logically it is

```
businessPriority.ratings.benefit
```

To represent this navigation path as a single relational column name I use the following:

```
business_priority_ratings_benefit
```

Of course, you can use another representative name if you like. Perhaps you prefer one that mixes camel case with underscores:

```
businessPriority_ratings_benefit
```

To your mind this sample notation may better express the navigation. I have standardized on all underscores since it leans more toward traditional SQL column names rather than object names. The corresponding MySQL database table definition includes the following columns:

```sql
CREATE TABLE `tbl_backlog_item` (
    ...
    `business_priority_ratings_benefit` int NOT NULL,
    `business_priority_ratings_cost` int NOT NULL,
    `business_priority_ratings_penalty` int NOT NULL,
    `business_priority_ratings_risk` int NOT NULL,
    ...
) ENGINE=InnoDB;
```

Together, the Hibernate mapping and relational database table definition provide both an optimal and queryable persistent object. Because Value attributes are denormalized into their parent Entity’s table row, there is no need for the database to use joins to retrieve even a deeply nested Value instance. When you specify an HQL query, Hibernate is able to easily map from the object expression of an object attribute into an optimal SQL query expression using a column, where

```
businessPriority.ratings.benefit
```

becomes

```
business_priority_ratings_benefit
```

Hence, although there is a clear impedance mismatch between objects and relational databases, we have realized one of the more functional and optimal mappings possible.

### 6.6.3 ORM and Many Values Serialized into a Single Column 多个值对象序列化到单个列中

There are unique challenges associated with mapping a collection of many Value Objects into a relational database using an ORM. To be clear, by collection I mean a List or Set that is held by an Entity and contains zero, one, or more Value instances. The challenges are not insurmountable, but the object-relational impedance mismatch becomes glaringly obvious here.

One option available with Hibernate object-relational mapping is to serialize the entire collection of objects into a textual representation and persist the representation into a single column. This approach has some drawbacks. However, in some cases the drawbacks are unobtrusive and may be summarily ignored in favor of leveraging this option’s advantages. In such cases you may decide to use this Value collection persistence option. Here are the potential drawbacks to consider:

- Column width. Sometimes you cannot determine the maximum number of Value elements in the collection, or the maximum size of each serialized Value. For example, some object collections could have any number of elements—an unknown upper limit. Also, each of the Value elements in the collection could have an indeterminate serialized representation character width. This can happen when one or more of the attributes of the Value type are of type String and the length in characters is many or open-ended. In either or both of these situations, it is possible that the serialized form or the entire collection would overflow the maximum available width of a character column. This may be further compounded by character columns that have a relatively narrow maximum width, or by the total maximum number of bytes available to store an entire row of data. While the MySQL InnoDB engine, for example, has a maximum VARCHAR width of 65,535 characters, it also has a limit of 65,535 total bytes of storage for a single row. You must allow room for enough columns to store an entire Entity. Oracle Database has a maximum VARCHAR2/NVARCHAR2 width of 4,000. If you cannot predetermine the maximum width required to store a serialized representation of a Value collection and/or your maximum column width could be overflowed, you should avoid this option.
- Must query. Since with this style Value collections are serialized into a flat text representation, the attributes of individual Value elements cannot be used in SQL query expressions. If any of the Value attributes must be queryable, you cannot use this option. It’s possible that this is a less likely reason to avoid this option since the need to query one or more attributes out of objects in a contained collection could be rare.
- Requires custom user type. To use this approach you must develop a Hibernate custom user type that manages serialization and deserialization of each collection. Personally, I think this is less obtrusive than the other concerns because a single, well-thought-out, custom user type implementation can support collections of every Value Object type (one size fits all).

I don’t provide a Hibernate custom user type here to manage collection serialization to a single column, but the Hibernate community provides plenty of guidance for you to implement your own.

### 6.6.4 ORM and Many Values Backed by a Database Entity 使用数据库实体保存多个值对象

A very straightforward approach to persisting a collection of Value instances using Hibernate (or other ORM) and a relational database is to treat the Value type as an entity in the data model. To reiterate what I asserted in the section “Reject Undue Influence of Data Model Leakage,” this approach must not lead to wrongly modeling a concept as an Entity in the domain model just because it is best represented as a database entity for the sake of persistence. It is the object-relation impedance mismatch that in some cases requires this approach, not a DDD principle. If there were a perfectly matched persistence style available to you, you’d model the concept as a Value type and never give database entity characteristics a second thought. It helps our domain modeling mind to think that way.

To accomplish this we can employ a Layer Supertype [Fowler, P of EAA]. Personally it makes me feel better to tuck away the necessary surrogate identity (primary key). However, since every Object in Java (and other languages) already has an internal unique identity that is used only by the virtual machine, you may feel justified in adding a specialized identity directly to the Value. I think whatever approach we prefer, when working around the object-relational impedance mismatch we need to formulate a convincing justification in our minds for why we make a technical choice. My preferences are addressed next.

Here’s an example of my preferred approach to surrogate keys, which uses two Layer Supertype classes:

```java
public abstract class IdentifiedDomainObject
        implements Serializable  {
    private long id= -1;
    public IdentifiedDomainObject() {
        super();
    }
    protected long id() {
        return this.id;
    }
    protected void setId(long anId) {
        this.id= anId;
    }
}
```

The first Layer Supertype involved is IdentifiedDomainObject. This abstract base class provides a basic surrogate primary key that is hidden from the view of clients. Because the accessor methods are declared protected, clients will never have to wonder if the methods are for their use. Of course, you can further avoid any knowledge of these methods by declaring their scope private. Hibernate has no problems using method or field reflection on any scope other than public.

Next, I provide one more Layer Supertype that is specific to Value Objects:

```java
public abstract class IdentifiedValueObject
        extends IdentifiedDomainObject  {
    public IdentifiedValueObject() {
        super();
    }
}
```

You may consider class IdentifiedValueObject as merely a marker class, a behaviorless subclass of IdentifiedDomainObject. I see it as having a source code documentation benefit because it makes the modeling challenge it addresses more explicit. Along those lines, class IdentifiedDomainObject has a second direct abstract subclass named Entity, which is discussed in Entities (5). I like this approach. You may prefer to eliminate these extra classes.

Now that there is a convenient and suitably hidden means to give any Value type a surrogate identity, here’s a sample class that puts it to use:

```java
public final class GroupMember extends IdentifiedValueObject  {
    private String name;
    private TenantId tenantId;
    private GroupMemberType type;

    public GroupMember(
            TenantId aTenantId,
            String aName,
            GroupMemberType aType) {
        this();
        this.setName(aName);
        this.setTenantId(aTenantId);
        this.setType(aType);
        this.initialize();
    }
    ...
}
```

Class GroupMember is a Value type that is collected by the Root Entity of the Aggregate class Group. The Root Entity contains any number of Group-Member instances. Now with each GroupMember instance being uniquely identified to the data model using its surrogate primary key, we are free to map its persistence as a database entity while keeping it a Value in the domain model. Here’s the relevant portion of class Group:

```java
public class Group extends Entity  {
    private String description;
    private Set<GroupMember> groupMembers;
    private String name;
    private TenantId tenantId;

    public Group(
            TenantId aTenantId,
            String aName,
            String aDescription) {
        this();
        this.setDescription(aDescription);
        this.setName(aName);
        this.setTenantId(aTenantId);
        this.initialize();
    }
    ...
    protected Group() {
        super();
        this.setGroupMembers(new HashSet<GroupMember>(0));
    }
    ...
}
```

Class Group will gradually build up any number of GroupMember instances in its Set of groupMembers. Keep in mind that if you will ever perform whole collection replacement, always use the Collection’s clear() method prior to replacement. Doing so ensures that the backing Hibernate Collection implementation will delete obsolete elements from the data store. The following is not an actual Group method, but an example provided to demonstrate how, in general, to avoid orphaned Value elements when performing whole collection replacement:

```java
public void replaceMembers(Set<GroupMember> aReplacementMembers) {
    this.groupMembers().clear();
    this.setGroupMembers(aReplacementMembers);
}
```

I think this ORM leakage into the model is unobtrusive because it uses a common Collection facility, and besides, the client doesn’t see it. Synchronizing collection contents with the database doesn’t always require careful thought. A single Value data store deletion is automatically covered by the use of Collection’s remove() method, so in that case there’s no ORM leakage at all.

Next, we are interested in the section of Group’s mapping description that maps the collection:

```xml
<hibernate-mapping>
    <class name="com.saasovation.identityaccess.domain.model.identity.Group" table="tbl_group" lazy="true">
        ...
        <set name="groupMembers" cascade="all,delete-orphan" inverse="false" lazy="true">
            <key column="group_id" not-null="true" />
            <one-to-many class="com.saasovation.identityaccess.domain.model.identity.GroupMember" />
        </set>
        ...
    </class>
</hibernate-mapping>
```

The Set of groupMembers is mapped exactly as a database entity. Additionally we see the full GroupMember mapping description:

```xml
<hibernate-mapping>
    <class name="com.saasovation.identityaccess.domain.model.identity.GroupMember" table="tbl_group_member" lazy="true">
        <id name="id" type="long" column="id" unsaved-value="-1">
            <generator class="native" />
        </id>
        <property name="name" column="name" type="java.lang.String" update="true" insert="true" lazy="false" />
        <component name="tenantId" class="com.saasovation.identityaccess.domain.model.identity.TenantId">
            <property name="id" column="tenant_id_id" type="java.lang.String" update="true" insert="true" lazy="false" />
        </component>
        <property name="type" column="type" type="com.saasovation.identityaccess.infrastructure.persistence.GroupMemberTypeUserType" update="true" insert="true" not-null="true" />
    </class>
</hibernate-mapping>
```

Note the `<id>` element that defines the persistence surrogate primary key. And finally, here is the corresponding MySQL tbl_group_member description:

```sql
CREATE TABLE `tbl_group_member` (
    `id` int(11) NOT NULL auto_increment,
    `name` varchar(100) NOT NULL,
    `tenant_id_id` varchar(36) NOT NULL,
    `type` varchar(5) NOT NULL,
    `group_id` int(11) NOT NULL,
    KEY `k_group_id` (`group_id`),
    KEY `k_tenant_id_id` (`tenant_id_id`),
    CONSTRAINT `fk_1_tbl_group_member_tbl_group`
         FOREIGN KEY (`group_id`) REFERENCES `tbl_group` (`id`),
    PRIMARY KEY (`id`)
) ENGINE=InnoDB;
```

When we look at the GroupMember mapping and database table description, we get the strong impression that we are dealing with an entity. There’s the primary key named id. There’s the separate table that must be joined with tbl_group. There’s the foreign key back to tbl_group. By any other name we are dealing with an entity, but only from the data model perspective. In the domain model GroupMember is clearly a Value Object. Appropriate steps have been taken in the domain model to carefully hide any persistence concerns. I give no clue to clients of the domain model that any persistence leakage has occurred. And what is more, even developers in the model must look hard to detect any notion of persistence leakage.

### 6.6.5 ORM and Many Values Backed by a Join Table 使用联合表保存多个值对象

Hibernate provides a means to persist multivalued collections in a join table without requiring the Value type itself to have any data model entity characteristics. This mapping type simply persists the collection Value elements to a dedicated table with the parent Entity domain object’s database identity as a foreign key. Thus, all collection Value elements can be queried by their parent’s foreign key identity and reconstituted into the model’s Value collection. The strength of this mapping approach is that the Value type doesn’t need to have a hidden surrogate identity in order to achieve a join. To use this Value collection mapping option you employ Hibernate’s `<composite-element>` tag.

This seems like a big win, and it may be for your needs. However, there are weaknesses to this approach that you should be aware of. One downside is that a join is necessary even if your Value type requires no surrogate key because it involves normalization of two tables. True, the “ORM and Many Values Backed by a Database Entity” approach also requires a join. But that approach is not limited by the second weakness of this one, which is . . .

If your collection is a Set, none of your Value type’s attributes may be null. This is the case because in order to delete (garbage collection in the data model) a given Set element, all attributes that make the element a unique Value must be used as a sort of composite key to find and delete it. A null cannot be used as a part of the required composite key. Of course, if you know that a given Value type will never have null attributes, this is a viable approach—that is, as long as you have no additional conflicting needs.

The third downside of using this mapping approach is that the Value type being mapped may itself not contain a collection. There is no provision for mapping with `<composite-element>` if the elements themselves contain collections. If your Value type does not hold a collection of any kind and otherwise meets the requirements for this mapping style, it is available for your use.

In the end, I find this mapping approach to be limiting enough that it deserves general avoidance. To me it is simply easier to put a well-hidden surrogate identity on the Value type that is collected into a one-to-many association and not worry about any of the `<composite-element>` constraints. You may feel differently, and it certainly can be leveraged to your benefit if all the modeling cards fall into place for you.

### 6.6.6 ORM and Enum-as-State Objects ORM 与枚举状态对象

If you find enums an effective modeling choice for Standard Types and/or State objects, you will need the means to persist them. With Hibernate, Java enums require a specialized persistence technique. Unfortunately to date, the Hibernate development community does not support enums as an out-of-the-box property type. Therefore, to persist enums in our model we have to create a Hibernate custom user type.

Recall that each GroupMember has a GroupMemberType:

```java
public final class GroupMember extends IdentifiedValueObject  {
    private String name;
    private TenantId tenantId;
    private GroupMemberType type;

    public GroupMember(
            TenantId aTenantId,
            String aName,
            GroupMemberType aType) {
        this();
        this.setName(aName);
        this.setTenantId(aTenantId);
        this.setType(aType);
        this.initialize();
    }
    ...
}
```

The GroupMemberType enum Standard Types include GROUP and USER. Here again is the definition:

```java
package com.saasovation.identityaccess.domain.model.identity;

public enum GroupMemberType {
    GROUP {
        public boolean isGroup() {
            return true;
        }
    },
    USER {
        public boolean isUser() {
            return true;
        }
    };
    public boolean isGroup() {
        return false;
    }
    public boolean isUser() {
        return false;
    }
}
```

The simple answer to persisting a Java enum Value is to store its text representation. However, the simple answer leads to the unfolding of a slightly more complex technique of creating a Hibernate customer user type. Rather than include here the various approaches to class EnumUserType provided by the Hibernate community, I provide the wiki article resource link: http://community.jboss.org/wiki/Java5EnumUserType.

At the time of writing, this wiki article provided a variety of approaches. There were samples for implementing a custom user type class for each enum type; a way to use Hibernate 3 parameterized types to avoid implementing a custom user for each enum type (very desirable); one that supports not only text string but numeric representations of the enum value; and even an enhanced implementation by Gavin King. Gavin King’s enhanced implementation allows the enum to be used as a type discriminator or as a data table identity (id).

Given the selection of one choice from these options, here’s an example of how the enum GroupMemberType is mapped:

```xml
<hibernate-mapping>
    <class name="com.saasovation.identityaccess.domain.model.↵
            identity.GroupMember" table="tbl_group_member" lazy="true">
        ...
        <property name="type" column="type" type="com.saasovation.identityaccess.infrastructure.persistence.GroupMemberTypeUserType" update="true" insert="true" not-null="true" />
    </class>
</hibernate-mapping>
```

Note that the `<property>` element’s type attribute is set to class Group-MemberTypeUserType’s full classpath. This is just one choice, and you should choose whatever one you prefer. Recall that the MySQL table description contains the column to hold the enum:

```sql
CREATE TABLE `tbl_group_member` (
    ...
    `type` varchar(5) NOT NULL,
    ...
) ENGINE=InnoDB;
```

The type column is a VARCHAR type with a maximum size of five characters, enough to hold the widest type text representation: GROUP or USER.

![](./figures/ch6/own_it.jpg)

## 6.7 WRAP-UP 本章小结

In this chapter you’ve seen the importance of favoring the use of Value Objects whenever possible, because they are simply easier to develop, test, and maintain.

- You’ve learned the characteristics of Value Objects and how to use them.
- You’ve seen how to leverage Value Objects to minimize integration complexity.
- You examined the use of domain Standard Types expressed as Values and have a few strategies for implementing them.
- You saw why SaaSOvation now favors modeling with Values whenever possible.
- You gained experience in how to test, implement, and persist Value types through the SaaSOvation projects.

Next, we’ll be looking at Domain Services, stateless operations that are actually part of the model.
