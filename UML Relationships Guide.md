# UML Relationships: Association, Aggregation, and Composition

A simple guide to understanding the three main relationships in UML class diagrams.

---

## Quick Summary

| Relationship | Symbol | Meaning | Lifecycle |
|-------------|--------|---------|-----------|
| **Association** | `———` | "knows about" | Independent |
| **Aggregation** | `◇———` | "has-a" (weak) | Independent parts |
| **Composition** | `♦———` | "owns" (strong) | Parts die with parent |

---

## 1. Association

**Definition:** A basic relationship where one class knows about another.

**UML Notation:**
```
Person ———————— Car
Person ——→ Car          (one-way)
Person ←——→ Car         (bi-directional)
```

**Code Example:**
```java
class Person {
    private Car car;  // Just a reference
    
    public void setCar(Car car) {
        this.car = car;
    }
}
```

**Key Points:**
- Simple reference between classes
- No ownership implied
- Both objects exist independently

---

## 2. Aggregation

**Definition:** "Has-a" relationship where parts can exist independently of the container.

**UML Notation:**
```
Department ◇———— Employee
```
*Hollow diamond on the container side*

**Real-World Examples:**
- `Library ◇—— Book` - Books exist without the library
- `Team ◇—— Player` - Players exist without the team
- `Playlist ◇—— Song` - Songs exist without the playlist

**Code Example:**
```java
class Employee {
    private String name;
    
    public Employee(String name) {
        this.name = name;
    }
}

class Department {
    private List<Employee> employees;
    
    // Employees created OUTSIDE and passed in
    public Department(List<Employee> employees) {
        this.employees = employees;
    }
    
    public void addEmployee(Employee emp) {
        employees.add(emp);  // Just adding reference
    }
}

// Usage:
Employee emp1 = new Employee("John");  // Created outside
Employee emp2 = new Employee("Jane");
Department dept = new Department(Arrays.asList(emp1, emp2));
// If dept is destroyed, emp1 and emp2 still exist
```

**Key Pattern:** 
- **Object in, store reference**
- Parts created outside
- Parts survive if container is destroyed

---

## 3. Composition

**Definition:** Strong ownership where parts cannot exist without the container.

**UML Notation:**
```
House ♦———— Room
```
*Filled diamond on the owner side*

**Real-World Examples:**
- `House ♦—— Room` - Rooms don't exist without the house
- `Book ♦—— Page` - Pages are part of the book
- `Order ♦—— OrderLine` - Order lines belong to the order

**Code Example:**
```java
class Room {
    private String name;
    private double area;
    
    public Room(String name, double area) {
        this.name = name;
        this.area = area;
    }
    
    @Override
    public String toString() {
        return name + " (" + area + " sq ft)";
    }
}

class House {
    private String address;
    private List<Room> rooms;
    
    public House(String address) {
        this.address = address;
        this.rooms = new ArrayList<>();
        
        // House creates its own rooms
        addRoom("Living Room", 200);
        addRoom("Kitchen", 150);
    }
    
    // Key: Pass attributes, not Room objects
    public void addRoom(String name, double area) {
        rooms.add(new Room(name, area));  // Created HERE
    }
    
    public void removeRoom(String name) {
        rooms.removeIf(room -> room.getName().equals(name));
    }
}

// Usage:
House house = new House("123 Main St");
house.addRoom("Bedroom", 180);  // Pass attributes
// House creates the Room internally
// When house is destroyed, rooms are destroyed too
```

**Key Pattern:**
- **Attributes in, object created inside**
- Parent creates the child
- Parent owns the lifecycle
- Parts destroyed when parent is destroyed

---

## The Core Difference in Code

### Aggregation (Pass the Object)
```java
Room bedroom = new Room("Bedroom", 200);  // Create OUTSIDE
house.addRoom(bedroom);                    // Pass object reference
```

### Composition (Pass the Attributes)
```java
house.addRoom("Bedroom", 200);  // Pass attributes
// House creates: new Room("Bedroom", 200) INSIDE
```

---

## Quick Decision Guide

**Ask yourself:**

1. **Just a reference?** → Use Association
   - `Person` has a reference to `Car`

2. **Container of existing things?** → Use Aggregation
   - `Playlist` contains `Songs` that exist independently
   - Objects passed from outside

3. **Owner that creates its parts?** → Use Composition
   - `House` owns `Rooms` that don't exist independently  
   - Objects created internally

---

## Real-World Production Example

```java
// Composition in action
class Order {
    private String orderId;
    private List<OrderLine> lines;
    
    public Order(String orderId) {
        this.orderId = orderId;
        this.lines = new ArrayList<>();
    }
    
    // Composition: Order creates OrderLines
    public void addItem(String product, int quantity, double price) {
        lines.add(new OrderLine(product, quantity, price));
    }
}

// Aggregation in action
class ShoppingCart {
    private List<Product> products;
    
    public ShoppingCart() {
        this.products = new ArrayList<>();
    }
    
    // Aggregation: Products exist independently
    public void addProduct(Product product) {
        products.add(product);  // Product created elsewhere
    }
}
```

---

## Remember

- **Composition is a design choice**, not a language restriction
- In practice, many relationships blur between aggregation and association
- **Composition** is the strongest form of ownership
- When in doubt, think about **who creates the object** and **what happens when the parent dies**

---

## Summary

| Aspect | Aggregation | Composition |
|--------|-------------|-------------|
| **Relationship** | Weak "has-a" | Strong "owns" |
| **Creation** | Objects passed in | Objects created inside |
| **Lifecycle** | Independent | Dependent on parent |
| **Code Pattern** | `add(Object obj)` | `add(String attr1, int attr2)` |
| **Example** | Library & Books | House & Rooms |
