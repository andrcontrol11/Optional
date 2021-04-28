#include <utility>

// Это исключение нужно генерировать в функции value
// при обращении к пустому объекту Optional
struct BadOptionalAccess {
};

template<typename T>
class Optional {
public:
    Optional() = default;

    Optional(const T &elem) {
        defined_ = true;
        new(data_) T(elem);
    }

    Optional(T && elem) {
        defined_ = true;
        new(data_) T(std::move(elem));
    }

    Optional(const Optional &other) {
        defined_ = other.defined_;
        if (defined_) {
            new(data_) T(other.value());
        }
    }
    Optional &operator=(const Optional &other) {
        if (other.defined_) {
            if (defined_) {
                value() = other.value();
            } else {
                new(data_) T(other.value());
                defined_ = true;
            }
        } else {
            reset();
        }
        return *this;
    }

    Optional &operator=(const T &elem) {
        if (defined_) {
            value() = elem;
        } else {
            defined_ = true;
            new(data_) T(elem);
        }
        return *this;
    }

    Optional &operator=(T &&elem) {
        if (defined_) {
            value() = std::move(elem);
        } else {
            defined_ = true;
            new(data_) T(std::move(elem));
        }
        return *this;
    }

    bool has_value() const {
        return defined_;
    }

    T &operator*() {
        return *reinterpret_cast<T *>(data_);
    }

    const T &operator*() const {
        return *reinterpret_cast<const T *>(data_);
    }

    T *operator->() {
        return reinterpret_cast<T *>(data_);
    }

    const T *operator->() const {
        return reinterpret_cast<const T *>(data_);
    }

    T &value() {
        if (defined_) {
            return *reinterpret_cast<T *>(data_);
        }
        throw BadOptionalAccess();
    }

    const T &value() const {
        if (defined_) {
            return *reinterpret_cast<const T *>(data_);
        }
        throw BadOptionalAccess();
    }

    void reset() {
        if (defined_) {
            value().~T();
            defined_ = false;
        }
    }

    ~Optional() {
        reset();
    }

private:
    // alignas нужен для правильного выравнивания блока памяти
    alignas(T) unsigned char data_[sizeof(T)];
    bool defined_ = false;
};
