class Animal {

    String type = print("Animal field");

    Animal() {
        System.out.println("Animal constructor");
    }

    String print(String message) {
        System.out.println(message);
        return message;
    }
}

class Dog extends Animal {

    String breed = print("Dog field");

    Dog() {
        System.out.println("Dog constructor");
    }
}