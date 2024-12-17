  12/12/2024 21:34

Tags: [[BASIC OOPS]]  [[System Design]] [[Design Patterns]]

Status: #solid_principles #lld 

# LLD

# Composition
![[Pasted image 20241217163558.png]]
An good example of a composition is the relationship between a Person and its Heart.
	When the person variable gets deleted then the heart variable inside the person also gets deleted
--
# Aggregation
![[Pasted image 20241217163627.png]]
Relationship between a person and its Home Address,  the home can belong to many person, where every person have a home

 ---
# Association

![[Pasted image 20241217163649.png]]
---
# Object Relationships
![[Pasted image 20241217163701.png]]
---
# UML Class Diagram

diagrams done before coding

- + for public
- - for private
- #for protected 
 ![[Pasted image 20241217163709.png]]
![[Pasted image 20241217163714.png]]

![[Pasted image 20241217163718.png]]

---

# Solid Principles #solid_principles 

- A class should have #S single responsibility
- Classes should be #O open for extension, bit closed for modification
		below code explains how to write extensible code
```cpp
#include <bits/stdc++.h>
using namespace std;

class StringFunction {
public:
  virtual string Edit(string str1, string str2) = 0; //pure virtual function
};

class AppendFunction : public StringFunction {
public:
  string Edit(string str1, string str2) override {
    return str1 + str2;
  }
};

class ReverseAppendFunction : public StringFunction {
public:
  string Edit(string str1, string str2) override {
    return str2 + str1;
  }
};


/*
  str1 + str1 + str2
*/
class CustomAppendFunction : public StringFunction {
public:
  string Edit(string str1, string str2) override {
    return str1 + str1 + str2;
  }
};


class TextEditor {
public:
  string Format(string str1, string str2, StringFunction& func) {
    return func.Edit(str1, str2);
  }
};



int main() {
  ios_base::sync_with_stdio(0);
  cin.tie(0); cout.tie(0);

  TextEditor editor;
  string str1, str2;
  cin >> str1 >> str2;

  AppendFunction append_function;
  cout << editor.Format(str1, str2, append_function) << endl;

  ReverseAppendFunction rev_append_function;
  cout << editor.Format(str1, str2, rev_append_function) << endl;

  CustomAppendFunction custom_function;
  cout << editor.Format(str1, str2, custom_function) << endl;

  return 0;
}
```
-  If S is a subtype of T, then objects of type T in a program may be replaced with objects of type S without altering any of the desirable properties of that program. #L Liskov Substitution
		below code says that Square should be a part of Rectangle and everywhere in the code wherever we replace rectangle with square, the code should run correctly
```cpp
#include <bits/stdc++.h>
using namespace std;

// T
class Rectangle {
public:
  Rectangle(int length, int width) : length_(length), width_(width) {}

  virtual int GetPerimeter() {
    return 2 * (length_ + width_);
  }

  virtual int GetArea() {
    return length_ * width_;
  }

private:
  int length_, width_;
};

// S
class Square : public Rectangle {
public:
  Square(int length) : Rectangle(length, length) {}
};

// Code using T
pair<int,int> GetRectangleInformation(Rectangle &rectangle) {
  return {rectangle.GetArea(), rectangle.GetPerimeter()};
}

int main() {
  ios_base::sync_with_stdio(0);
  cin.tie(0); cout.tie(0);

  Rectangle rectangle(4, 2);
  auto it = GetRectangleInformation(rectangle);
  cout << it.first << " " << it.second << endl;

  Square square(5);
  it = GetRectangleInformation(square);
  cout << it.first << " " << it.second << endl;

  return 0;
}
```


-  #I Interface Segregation, Clients should not be forced to depend on methods that they do not use.
		the below code explains that for square if we don't declare a separate class like 2DShapeInterface then the code wont compile, as for square GetVolume won't be defined
```cpp
#include <bits/stdc++.h>
using namespace std;

// Interface
class TwoDimentionShapeInterface {
public:
  virtual double GetArea() = 0;
};

class ThreeDimentionShapeInterface {
public:
  virtual double GetArea() = 0;
  
  virtual double GetVolume() = 0;
};

// Client1
class Square : public TwoDimentionShapeInterface {
public:
  Square(int len) : len_(len) {}

  double GetArea() override { return len_ * len_; }

private:
  int len_;
};

// Client2
class Sphere : public ThreeDimentionShapeInterface {
public:
  Sphere(int radius) : radius_(radius) {}

  double GetArea() override {
    return 4 * 3.14 * radius_ * radius_;
  }

  double GetVolume() override {
    return 4 * 3.14 * radius_ * radius_ * radius_ / 3;
  }

private:
  int radius_;
};

int main() {
  ios_base::sync_with_stdio(0);
  cin.tie(0); cout.tie(0);

  Sphere sphere(2);
  cout << sphere.GetArea() << " " << sphere.GetVolume() << endl;

  Square square(5);
  cout << square.GetArea() << endl;

  return 0;
}
```

 - #D Dependency Inversion: High level modules should not depend on low-leveled modules. Both should depend on the abstraction.
	 Abstractions should not depend upon details. Details should depend upon abstractions (interface code).

 ![[Pasted image 20241217163731.png]]