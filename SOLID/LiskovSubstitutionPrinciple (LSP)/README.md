## Liskov Substitution Principle (LSP)

**Definition:**
The Liskov Substitution Principle states that a child class which overrides a function must ensure that this function reflects the same behavior as the base class function. The derived class must accomplish a strict "IS-A" relationship to consider using inheritance. As the principle says: *"Derived or child classes must be able to replace their base or parent classes seamlessly."*

**Example & Violation:**
Consider a function that receives images inside a frame of type `Rectangle`. This function is responsible for zooming and doubling the width (x2). 

```cpp
void doubleTheWidth(Rectangle& rect) {
    rect.setWidth(rect.getWidth() * 2);
    // Expectation: Only the width doubles, while the height remains unchanged.
}
 
```
We have a `Rectangle` class as the base class and a `Square` class as the derived class. Each class has its own `setWidth()` and `setHeight()` methods. 

If this function receives a `Rectangle` reference but the actual object passed is of type `Square`, the function is going to double **both** the height and the width because of how the square overrides the methods. This completely violates the **LSP** because the behavior is no longer consistent with what the function expects from a rectangle.

**The Solution:**
To solve this and strictly enforce the principle, we should not force a false inheritance. Instead, we create a base class called `Shape`, and then let both `Rectangle` and `Square` extend it independently. This way, each class can do its own stuff without breaking the expected behavior of one another.

```cpp
class Shape {
public:
    virtual int getArea() = 0;
};

class Rectangle : public Shape { /* Independent width/height logic */ };
class Square : public Shape { /* Independent side-length logic */ };
  
```
Note: LSP can also be violated if a subclass changes the expected return types or behaviors of the base class. For example, if a base class function is expected to return a string, but the subclass overrides it to return an integer, a double, or throws an unexpected exception, it breaks the program's correctness and violates the principle.  