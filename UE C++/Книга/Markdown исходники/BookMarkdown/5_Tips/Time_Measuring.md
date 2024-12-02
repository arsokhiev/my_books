# Замер времени исполнения кода
Иногда надо узнать, сколько времени исполнялся тот или иной код.
В Unreal это делается так:
```cpp
double start = FPlatformTime::Seconds(); // Time Measuring
// ...
// тут ваш код
// ...
double end = FPlatformTime::Seconds(); // Time Measuring
UE_LOG(LogTemp, Warning, TEXT("code executed in %f seconds."), end - start);
```
В чистом с++ это можно сделать так:
```cpp
#include <iostream>
#include <stdlib.h>
#include <functional> // std::function
#include <stdio.h> // printf
#include <time.h> // clock
int main(){
    clock_t start = clock(); // measure time start
    // prime lambda
    std::function<bool(int)> IsPrime = [](int Num) -> bool {
					// since 0 and 1 is not prime return false.
					if (Num < 2){
						return false;
                    }
					// Run a loop from 2 to n-1
					for (int i = 2; i < Num; i++) {
						// if the number is divisible by i, then n is not a
						// prime number.
						if (Num % i == 0){
							return false;
                        }
					}
					// otherwise, n is prime number.
					return true;
				};
 
    int PrimesFound = 0;
    int CurrentTestNumber = 0;
    // Generate prime numbers until their amount less than 10000
    while (PrimesFound < 10000)
    {
        CurrentTestNumber++;
        if (IsPrime(CurrentTestNumber)) {
            PrimesFound++;
        }
    }
    printf ("Primes Found = %i in calculations Until Number = %i", PrimesFound, CurrentTestNumber);
    clock_t end = clock(); // measure time end
    float seconds = (float)(end - start) / CLOCKS_PER_SEC;
    printf ("\nYour calculations took %.2lf seconds to run.\n", seconds );
    return 0;
}
```