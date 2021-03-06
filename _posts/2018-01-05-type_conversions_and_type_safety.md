--- 
layout: post 
title: "Type Conversions and Type Safety" 
date: 2018-01-05 13:35:54 
author: Dewey Mao 
categories: C/C++ 
--- 

A program that never explicitly or implicitly converts values from one type to another is type-safe by definition. 
However, type conversions, even unsafe conversions, are sometimes required. 
Please use explicit conversions(casts) as much as possible.

### 1 static_cast
For casts that are checked at compile time only. static_cast returns an error if the compiler detects that you are trying to cast between types that are completely incompatible. 
{% highlight c++ lineos %}
double d = 1.58947;  
int i = d;  // warning C4244 possible loss of data  
int j = static_cast<int>(d);       // No warning.  
string s = static_cast<string>(d); // Error C2440:cannot convert from  
                                   // double to std:string  
{% endhighlight %}

### 2 dynamic_cast
For safe, runtime-checked casts of pointer-to-base to pointer-to-derived. 
{% highlight c++ lineos %}
Base* b = new Base();  

// Run-time check to determine whether b is actually a Derived*  
Derived* d3 = dynamic_cast<Derived*>(b);  

// If b was originally a Derived*, then d3 is a valid pointer.  
if(d3)  
{  
   // Safe to call Derived method.  
   cout << d3->DoSomethingMore() << endl;  
}  
else  
{  
   // Run-time check failed.  
   cout << "d3 is null" << endl;   
}  
{% endhighlight %}

### 3 const_cast
For casting away the const-ness of a variable, or converting a non-const variable to be const. 
{% highlight c++ lineos %}
void Func(double& d) { ... }  
void ConstCast()  
{  
   const double pi = 3.14;  
   Func(const_cast<double&>(pi)); //No error.  
}  
{% endhighlight %}

### 4 reinterpret_cast
For casts between unrelated types. This cast operator is not used as often as the others, and it's not guaranteed to be portable to other compilers.

### 5 Reference
<a href="https://docs.microsoft.com/en-us/cpp/cpp/type-conversions-and-type-safety-modern-cpp" target="_blank"> Type Conversions and Type Safety (Modern C++) </a>