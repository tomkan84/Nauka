# Fabryka Abstrakcyjna

### Link do artykułu --> [RefactoringGuru](https://refactoring.guru/pl/design-patterns/abstract-factory)

## 1. Cel

Tworzenie spokrewnionych ze sobą obiektów bez potrzeby deklarowania nowych klas

## 2. Kod
```cpp
#include <iostream>
#include <memory>
#include <format>
#include <iomanip>

class IChair {
   public:
    virtual ~IChair() noexcept {}

    virtual bool HasLegs() = 0;
    virtual void SitOn() = 0;
};

class VictorianChair : public IChair {
   public:
    bool HasLegs() override { return true; }
    void SitOn() override {
        std::cout << "Sitting on Victorian style Chair B)\n";
    }
};

class ModernChair : public IChair {
   public:
    bool HasLegs() override { return false; }
    void SitOn() override { std::cout << "Sitting on Modern style Chair B)\n"; }
};

class IFurnitureFactory {
   public:
    virtual ~IFurnitureFactory() noexcept {}

    virtual std::shared_ptr<IChair> CreateChair() = 0;
};

class VictorianFurnitureFactory : public IFurnitureFactory {
   public:
    std::shared_ptr<IChair> CreateChair() override {
        return std::make_shared<VictorianChair>();
    }
};

class ModernFurnitureFactory : public IFurnitureFactory {
    public:
    std::shared_ptr<IChair> CreateChair() override { 
        return std::make_shared<ModernChair>();
    }
};

int main() {
    VictorianFurnitureFactory victorianFactory{};
    ModernFurnitureFactory modernFactory{};

    std::shared_ptr<IChair> myVictorianChair{ victorianFactory.CreateChair() };
    std::shared_ptr<IChair> myModernChair{ modernFactory.CreateChair() };

    std::cout << std::boolalpha;

    std::cout << std::format("Victorian chair: \tHasLegs() = {}\n", myVictorianChair->HasLegs());
    std::cout << std::format("Modern chair: \t\tHasLegs() = {}\n", myModernChair->HasLegs());
}
```

## Kod rozszerzony o wybór użytkownika
```cpp
#include <format>
#include <iomanip>
#include <iostream>
#include <memory>

class IChair {
   public:
    virtual ~IChair() noexcept {}

    virtual bool HasLegs() = 0;
    virtual void SitOn() = 0;
};

class VictorianChair : public IChair {
   public:
    bool HasLegs() override { return true; }
    void SitOn() override {
        std::cout << "Sitting on Victorian style Chair B)\n";
    }
};

class ModernChair : public IChair {
   public:
    bool HasLegs() override { return false; }
    void SitOn() override { std::cout << "Sitting on Modern style Chair B)\n"; }
};

class IFurnitureFactory {
   public:
    virtual ~IFurnitureFactory() noexcept {}

    virtual std::shared_ptr<IChair> CreateChair() = 0;
};

class VictorianFurnitureFactory : public IFurnitureFactory {
   public:
    std::shared_ptr<IChair> CreateChair() override {
        return std::make_shared<VictorianChair>();
    }
};

class ModernFurnitureFactory : public IFurnitureFactory {
   public:
    std::shared_ptr<IChair> CreateChair() override {
        return std::make_shared<ModernChair>();
    }
};

enum class FurnitureFactoryType : uint32_t { VICTORIAN = 1, MODERN };

std::unique_ptr<IFurnitureFactory> CreateFactory(FurnitureFactoryType type) {
    switch (type) {
        case FurnitureFactoryType::VICTORIAN:
            return std::make_unique<VictorianFurnitureFactory>();
        case FurnitureFactoryType::MODERN:
            return std::make_unique<ModernFurnitureFactory>();
    }
}

FurnitureFactoryType GetUserInput(std::string_view msg) {
    std::cout << msg;
    uint32_t userChoice{};
    std::cin >> userChoice;
    return static_cast<FurnitureFactoryType>(userChoice);
}

int main() {
    std::cout << "Wybierz typ mebli: \n";

    std::cout << "\t 1. Wiktoriański\n";
    std::cout << "\t 2. Nowoczesny\n";

    const FurnitureFactoryType choice{ GetUserInput("Wybor: ") };
    std::unique_ptr<IFurnitureFactory> factory{ CreateFactory(choice) };

    std::shared_ptr<IChair> chosenChair{ factory->CreateChair() };
    chosenChair->SitOn();
}
```
