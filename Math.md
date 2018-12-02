


### 1. largest common factor
    - if a or b is zero, we return non-zero elements, because everything divides zero
```
public int gcd(int a, int b) { 
    // Everything divides 0  
    if (a == 0) 
      return b; 
    if (b == 0) 
      return a; 

    // base case 
    if (a == b) 
        return a; 

    // a is greater 
    if (a > b) 
        return gcd(a-b, b); 
    return gcd(a, b-a); 
 } 
```
