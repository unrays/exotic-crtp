# exotic-crtp

A modern CRTP pattern for C++23 static polymorphism.

**Article:** Rethinking Static Polymorphism with C++23  
https://medium.com/@felixolivierdumas/exotic-crtp-rethinking-static-polymorphism-with-c-23-89f9e75e8ffd
(the article is not updated to the latest technical modifications, some parts needs to be completely rewritten)

## Reference Implementation
```cpp
// Copyright (c) May 2026 Félix-Olivier Dumas. All rights reserved.
// Licensed under the terms described in the LICENSE file

// Reference example of the pattern
// See: https://medium.com/@felixolivierdumas/exotic-crtp-rethinking-static-polymorphism-with-c-23-89f9e75e8ffd

#pragma once

#include <iostream>
#include <type_traits>
#include <utility>

namespace exotic {

    template<typename From>
    struct crtp_access : From {};

    template<typename T>
    constexpr decltype(auto) as_crtp(T&& obj) noexcept {
        using crtp_access_t = crtp_access<std::remove_cvref_t<T>>;
        return static_cast<crtp_access_t&&>(obj);
    }

}

struct Base {
    void interface(this auto&& self) {
        exotic::as_crtp(self).implementation();
    }
};

struct Derived : Base {
    void implementation(this exotic::crtp_access<Derived> self) {
        std::cout << "Derived implementation" << std::endl;
    }
};

int main() {
    Derived d;
  
    d.interface(); // perfectly works
  
    //d.implementation(); -> doesn't work, Derived only exposes .interface()
}
```
