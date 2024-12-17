11/12/2024 09:16

Tags:[[00_DSA]]

Status:

# BASIC OOP

- while declaring variables inside a class keep a underscore __ at the end because that is the industry standard, like name_ , age_  .
- class name should begin with capital letter
- variable names should be all lower case in private
- names in public should follow snake_case
- when the main function returned all the destructor functions of the classes are called.
- NO concept of garbage collector in c++
- if an object is caled inside a function oustide the main and class, then when the function call is over or it is finished then the destructor of the object is called![[Pasted image 20241217163948.png]]
-   In c++ there is a concept of scope i.e every object created inside { } will exit or get deleted when the scope } ends.
```cpp
{
	A obj(4);
}
//this will get deleted after } i.e its destructor will be called
```


how to initialize constructors members
```cpp
class A{
	public:
		A(int a, int b) : a_(a), b_(b) {} 
	private:
		int a_;
		int b_; //initialized in the order they are declared in the class i.e first a_ then b_, it dosent matter which is initialized first in the constructor function.
};
```

 - if B is inherited from  A then
	 `constructor of A is called-> then B is called-> Destructor of B is called-> then A destructor is called`

- Protected access specifier can be accessed bu both from within the class and by the inherited/ derived class

![[Pasted image 20241217164003.png]]

 when inherited class is type of private, then all the inherited properties are of type private, hence can't be accessed outside the class![[Pasted image 20241217164014.png]]
private properties are private everywhere, so can never be accessed outside    

for POLYMORPHISM both `&` and `virtual` keywords are very important in the calling function and the base class respectively.

```cpp
int a=10;
int &b=a;
b=15; //this changes the value of a to 15, because now b points to the same location as of a, because b is a reference 
```
![[Pasted image 20241217164026.png]]
since we have used base class pointer here, we can't access derived class objects here
![[Pasted image 20241217164036.png]]
by including the virtual keyword now we can access derived class objects
- Now lets say you call the destructor for B, then it will only delete the Base class B and not derived class D, this is called `memory leak`, hence you need to put `virtual` in the destructor of the base class, then when called it will delete both derived class and the base class respectively. 
	- Therefore always make your destructor virtual, to avoid memory leaks

>[!note]
>If there are multiple cascading derived classes, and there is virtual function in the base class, then when that function is called, the last (latest) derived class function is called, even if other derived classes don't have virtual written before their function
override and final are type check functionalities in C++, without them code will run, but this ensures that what has been decided earlier is not compromised later

 ![[Pasted image 20241217164049.png]]
When we use 
```cpp
Animal Ani=new Animal(); //here its allocated on Heap
```
but when we
```cpp
Animal Ani; //here its allocated on stack, hence deleted after the main function is exited
```
![[Pasted image 20241217164056.png]]
#### Object Slicing

![[Pasted image 20241217164119.png]]

Since here we are not referencing B as &B, here B is an actual object hence it ignores the derived class of D, and only keeps the base class A.

A pure virtual function is always meant to be overridden, hence you can't create objects of it
 ```cpp
 class Animal{
	 virtual void Sound()=0;
	};
```

Any class with one or more pure virtual function is called a abstract base class, which means taht it cannot be instantiated



 ![[Pasted image 20241217164139.png]]