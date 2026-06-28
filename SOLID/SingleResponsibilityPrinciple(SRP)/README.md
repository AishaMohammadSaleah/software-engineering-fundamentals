## Single Responsibility Principle (SRP)
**Definition:**
Every code entity (class, interface, or function) must have only one responsibility or functionality, note that (Responsibility ≠ Action). As Uncle Bob states: "A class should have one, and only one, reason to change," meaning that a class or a function should focus on doing exactly one job.

**Example & Violation:**
Consider an AreaCalculator class with a sumArea() function. If this function is responsible for determining the type of each shape (using if/else or switch blocks) and then calculating the area for squares, rectangles, and circles all in the same block, it heavily violates SRP. The function is taking on too many responsibilities (knowing the logic of every single shape).

**The Solution:**
To comply with SRP, we must separate every shape's logic from the summation function. Each shape (e.g., Square, Circle) should be responsible for its own area calculation logic. The sumArea() function should only have one job: summing up the pre-calculated areas, without caring about how each shape computed its area.

```cpp
class Square {
public:
    int side;
    int calculateArea() { return side * side; }
};

class Circle {
public:
    int radius;
    int calculateArea() { return 3.14 * radius * radius; }
};

class AreaCalculator {
public:
    int sumArea(vector<Square>& squares, vector<Circle>& circles) {
        int total = 0;
        for (auto& s : squares) total += s.calculateArea();
        for (auto& c : circles) total += c.calculateArea();
        return total;
    }
};