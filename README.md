# exotic-crtp

# The article : https://medium.com/@felixolivierdumas/exotic-crtp-rethinking-static-polymorphism-with-c-23-89f9e75e8ffd

## The pattern
```cpp
namespace exotic {
    template<typename... From>
    struct crtp_access : From... {};

    template<typename T>
    constexpr decltype(auto) as_crtp(T&& obj) noexcept {
        using crtp_access_t = crtp_access<std::remove_cvref_t<T>>;
        return static_cast<crtp_access_t&&>(obj);
    }
}
   
struct Base {
    void interface(this auto&& self) {
        return as_crtp(self).implementation();
    }
};

struct Derived : Base {
    void implementation(this crtp_access<Derived> self) {
        std::cout << "Derived implementation" << std::endl;
    }
};
```
