---
title: "用Jackson反序列化JSON成多态类型，一个简单的实例"
categories: [ "Java" ]
tags: [ "json","deserialize","jackson" ]
draft: false
slug: "deserialize-json-with-jackson-into"
date: "2014-11-07 12:20:00"
---

## Jackson API 版本说明 ##
下面的代码示例是使用Jackson 1.7编写的。x或1.8.x。他们可能工作与Jackson 原有1.9。x,但Jackson 2.0介绍了API变化足够重要,这段代码不能编译。在初步审查,看来包名称改变,至少一个方法签名更改或移动或重命名或不再存在。在不久的将来,我可能会发布更新的代码示例使用Jackson 2. x。


<!--more-->


### 这篇文章存在的原因 ###
我最近在StackOverflow.com上回答问题的反序列化JSON 多态类型（polymorhpic）在Java中,我找不到一个简单而完整的示例使用Jackson 。在撰写本文时,官方文档的主题描述方面如何做到这一点,但它没有一个完整的例子。搜索其他资源还没有出现任何完整的例子。

以下是反序列化Jackson的例子,建立一个完整的示例Java中填充一个多态数据结构。前三个例子不涉及多态性。介绍了多态性在第四的例子。
查阅杰克逊解释每个组件的API文档。

### 示例1:简单对象序列化和反序列化 ###


    // input and output:
    //   {"type":"dog","name":"Spike"}
    
    import org.codehaus.jackson.map.ObjectMapper;
    
    public class Foo
    {
      static String jsonInput = 
          "{\"type\":\"dog\",\"name\":\"Spike\"}";
    
      public static void main(String[] args) throws Exception
      {
        ObjectMapper mapper = new ObjectMapper();
        Animal animal = mapper.readValue(jsonInput, Animal.class);
        System.out.println(mapper.writeValueAsString(animal));
      }
    }
    
    class Animal
    {
      public String type;
      public String name;
    }

### 示例 2： 简单集合反序列化和序列化 ###

    // input and output:
    //   [
    //     {"type":"dog","name":"Spike"},
    //     {"type":"cat","name":"Fluffy"}
    //   ]
    
    import java.io.File;
    import java.util.Collection;
    
    import org.codehaus.jackson.map.ObjectMapper;
    import org.codehaus.jackson.map.type.TypeFactory;
    import org.codehaus.jackson.type.TypeReference;
    
    public class Foo
    {
      public static void main(String[] args) throws Exception
      {
        ObjectMapper mapper = new ObjectMapper();
        Collection<Animal> animals =
          mapper.readValue(new File("input_2.json"),
            new TypeReference<Collection<Animal>>() {});
        System.out.println(mapper.writeValueAsString(animals));
        // or
        Collection<Animal> animals2 =
          mapper.readValue(new File("input_2.json"),
            TypeFactory.defaultInstance().constructParametricType(
                Collection.class, Animal.class));
        System.out.println(mapper.writeValueAsString(animals2));
      }
    }
    
    class Animal
    {
      public String type;
      public String name;
    }

### 示例 3： 简单反序列化/序列化 从对象（Container Object ）到集合（Collection） ###

    // input and output:
    //   {
    //     "animals":
    //     [
    //       {"type":"dog","name":"Spike"},
    //       {"type":"cat","name":"Fluffy"}
    //     ]
    //   }
    
    import java.io.File;
    import java.util.Collection;
    
    import org.codehaus.jackson.map.ObjectMapper;
    
    public class Foo
    {
      public static void main(String[] args) throws Exception
      {
        ObjectMapper mapper = new ObjectMapper();
        Zoo zoo = 
          mapper.readValue(new File("input_3.json"), Zoo.class);
        System.out.println(mapper.writeValueAsString(zoo));
      }
    }
    
    class Zoo
    {
      public Collection<Animal> animals;
    }
    
    class Animal
    {
      public String type;
      public String name;
    }

### 示例 4： 简单反序列化/序列化到/从容器对象与多态集合 ###

    // input and output:
    //   {
    //     "animals":
    //     [
    //       {"type":"dog","name":"Spike","breed":"mutt",
    //           "leash_color":"red"},
    //       {"type":"cat","name":"Fluffy",
    //           "favorite_toy":"spider ring"}
    //     ]
    //   }
    
    import java.io.File;
    import java.util.Collection;
    
    import org.codehaus.jackson.annotate.JsonSubTypes;
    import org.codehaus.jackson.annotate.JsonSubTypes.Type;
    import org.codehaus.jackson.annotate.JsonTypeInfo;
    import org.codehaus.jackson.map.ObjectMapper;
    
    public class Foo
    {
      public static void main(String[] args) throws Exception
      {
        ObjectMapper mapper = new ObjectMapper();
        mapper.setPropertyNamingStrategy(
            new CamelCaseNamingStrategy());
        Zoo zoo = 
          mapper.readValue(new File("input_4.json"), Zoo.class);
        System.out.println(mapper.writeValueAsString(zoo));
      }
    }
    
    class Zoo
    {
      public Collection<Animal> animals;
    }
    
    @JsonTypeInfo(
        use = JsonTypeInfo.Id.NAME,
        include = JsonTypeInfo.As.PROPERTY,
        property = "type")
    @JsonSubTypes({
        @Type(value = Cat.class, name = "cat"),
        @Type(value = Dog.class, name = "dog") })
    abstract class Animal
    {
      public String name;
    }
    
    class Dog extends Animal
    {
      public String breed;
      public String leashColor;
    }
    
    class Cat extends Animal
    {
      public String favoriteToy;
    }

### 示例 5： 简单反序列化/序列化与 MixIn 到/从容器对象（Container Object）与多态集合（Polymorphic Collection）###

    // input and output:
    //   {
    //     "animals":
    //     [
    //       {"type":"dog","name":"Spike","breed":"mutt",
    //           "leash_color":"red"},
    //       {"type":"cat","name":"Fluffy",
    //           "favorite_toy":"spider ring"}
    //     ]
    //   }
    
    import java.io.File;
    import java.util.Collection;
    
    import org.codehaus.jackson.annotate.JsonSubTypes;
    import org.codehaus.jackson.annotate.JsonTypeInfo;
    import org.codehaus.jackson.annotate.JsonSubTypes.Type;
    import org.codehaus.jackson.map.ObjectMapper;
    
    public class Foo
    {
      public static void main(String[] args) throws Exception
      {
        ObjectMapper mapper = new ObjectMapper();
        mapper.setPropertyNamingStrategy(
            new CamelCaseNamingStrategy());
        mapper.getDeserializationConfig().addMixInAnnotations(
            Animal.class, PolymorphicAnimalMixIn.class);
        mapper.getSerializationConfig().addMixInAnnotations(
            Animal.class, PolymorphicAnimalMixIn.class);
        Zoo zoo = 
          mapper.readValue(new File("input_5.json"), Zoo.class);
        System.out.println(mapper.writeValueAsString(zoo));
      }
    }
    
    class Zoo
    {
      public Collection<Animal> animals;
    }
    
    @JsonTypeInfo(
        use = JsonTypeInfo.Id.NAME,
        include = JsonTypeInfo.As.PROPERTY,
        property = "type")
    @JsonSubTypes({
        @Type(value = Cat.class, name = "cat"),
        @Type(value = Dog.class, name = "dog") })
    abstract class PolymorphicAnimalMixIn
    {
      
    }
    
    abstract class Animal
    {
      public String name;
    }
    
    class Dog extends Animal
    {
      public String breed;
      public String leashColor;
    }
    
    class Cat extends Animal
    {
      public String favoriteToy;
    }

### 示例 6： 没有容器对象与多态的集合的类型元素（Type Element）的简单反序列化过程 ###
一些真实世界的 JSON Api 有多态类型的成员，但不包括 （不像在前面的示例 JSON） 的类型元素。成多态集合进行反序列化此类来源是涉及更多一点。以下是一个相对简单的解决方案。（此示例包括后续序列化反序列化的 Java 结构回去输入 JSON，但序列化是相当令人乏味。)

    // input and output:
    //   {
    //     "animals":
    //     [
    //       {"name":"Spike","breed":"mutt","leash_color":"red"},
    //       {"name":"Fluffy","favorite_toy":"spider ring"},
    //       {"name":"Baldy","wing_span":"6 feet",
    //           "preferred_food":"wild salmon"}
    //     ]
    //   }
    
    import java.io.File;
    import java.io.IOException;
    import java.util.Collection;
    import java.util.HashMap;
    import java.util.Iterator;
    import java.util.Map;
    import java.util.Map.Entry;
    
    import org.codehaus.jackson.JsonNode;
    import org.codehaus.jackson.JsonParser;
    import org.codehaus.jackson.JsonProcessingException;
    import org.codehaus.jackson.Version;
    import org.codehaus.jackson.map.DeserializationContext;
    import org.codehaus.jackson.map.ObjectMapper;
    import org.codehaus.jackson.map.deser.StdDeserializer;
    import org.codehaus.jackson.map.module.SimpleModule;
    import org.codehaus.jackson.node.ObjectNode;
    
    import fubar.CamelCaseNamingStrategy;
    
    public class Foo
    {
      public static void main(String[] args) throws Exception
      {
        AnimalDeserializer deserializer = 
            new AnimalDeserializer();
        deserializer.registerAnimal("leash_color", Dog.class);
        deserializer.registerAnimal("favorite_toy", Cat.class);
        deserializer.registerAnimal("wing_span", Bird.class);
        SimpleModule module =
          new SimpleModule("PolymorphicAnimalDeserializerModule",
              new Version(1, 0, 0, null));
        module.addDeserializer(Animal.class, deserializer);
        
        ObjectMapper mapper = new ObjectMapper();
        mapper.setPropertyNamingStrategy(
            new CamelCaseNamingStrategy());
        mapper.registerModule(module);
    
        Zoo zoo = 
            mapper.readValue(new File("input_6.json"), Zoo.class);
        System.out.println(mapper.writeValueAsString(zoo));
      }
    }
    
    class AnimalDeserializer extends StdDeserializer<Animal>
    {
      private Map<String, Class<? extends Animal>> registry =
          new HashMap<String, Class<? extends Animal>>();
    
      AnimalDeserializer()
      {
        super(Animal.class);
      }
    
      void registerAnimal(String uniqueAttribute,
          Class<? extends Animal> animalClass)
      {
        registry.put(uniqueAttribute, animalClass);
      }
    
      @Override
      public Animal deserialize(
          JsonParser jp, DeserializationContext ctxt) 
          throws IOException, JsonProcessingException
      {
        ObjectMapper mapper = (ObjectMapper) jp.getCodec();
        ObjectNode root = (ObjectNode) mapper.readTree(jp);
        Class<? extends Animal> animalClass = null;
        Iterator<Entry<String, JsonNode>> elementsIterator = 
            root.getFields();
        while (elementsIterator.hasNext())
        {
          Entry<String, JsonNode> element=elementsIterator.next();
          String name = element.getKey();
          if (registry.containsKey(name))
          {
            animalClass = registry.get(name);
            break;
          }
        }
        if (animalClass == null) return null;
        return mapper.readValue(root, animalClass);
      }
    }
    
    class Zoo
    {
      public Collection<Animal> animals;
    }
    
    abstract class Animal
    {
      public String name;
    }
    
    class Dog extends Animal
    {
      public String breed;
      public String leashColor;
    }
    
    class Cat extends Animal
    {
      public String favoriteToy;
    }
    
    class Bird extends Animal
    {
      public String wingSpan;
      public String preferredFood;
    }

FINI。0.0