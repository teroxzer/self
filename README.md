# self
## Tractatus logico-selfidiomicus a.k.a. C++ self idiom
Whereof one cannot this, thereof one must be self.
```
/////////////////////////////////////////////////////////////////////////////

class Value final
{
public:

	Value() noexcept = default;

	explicit Value(int x) noexcept : self { x, 1, 1 }
	{}

	Value(int x, int y) noexcept : self
	{
		.x = x,
		.y = y,
		.z = 2
	}
	{}

	auto x() const noexcept -> int
	{
		return self.x;
	}

	auto y(int y) noexcept -> Value&
	{
		self.y = y;
		self.z = y < 0 ? -y : y;

		return *this;
	}

	auto z(int z) const noexcept -> int
	{
		return self.x * self.y * self.z + z < 0 ? -z : z;
	}

private:

	struct Self final
	{
		int x {};
		int y {};
		int z {};
	}
	self;
};

/////////////////////////////////////////////////////////////////////////////

class Pimpl final
{
public:

	 Pimpl();
	 Pimpl(Pimpl const&);
	 Pimpl(Pimpl&&) noexcept;
	~Pimpl() noexcept;

	explicit Pimpl(int x);
	         Pimpl(int x, int y);

	auto operator = (Pimpl const&)     -> Pimpl&;
	auto operator = (Pimpl&&) noexcept -> Pimpl&;

	auto x ()      const noexcept -> int;
	auto y (int y)       noexcept -> Pimpl&;
	auto z (int z) const noexcept -> int;

private:

	struct Self;
	struct Self* self {};
};

/////////////////////////////////////////////////////////////////////////////

struct Pimpl::Self final
{
	int x {};
	int y {};
	int z {};

	static auto setZ (Self*, int* z)      noexcept -> void;
	static auto getZ (Self const*, int z) noexcept -> int;
};

auto Pimpl::Self::setZ(Self* self, int* z) noexcept -> void
{
	if(z)
	{
		self->z = *z < 0 ? -*z : *z;
	}
}

auto Pimpl::Self::getZ(Self const* self, int z) noexcept -> int
{
	return self->x * self->y * self->z + z;
}

Pimpl::Pimpl() : self { new Self }
{}

Pimpl::Pimpl(Pimpl const& other) : self { new Self { *other.self } }
{}

Pimpl::Pimpl(Pimpl&& other) noexcept : self { other.self }
{
	other.self = nullptr;
}

Pimpl::Pimpl(int x) : self { new Self { x, 1, 1 } }
{}

Pimpl::Pimpl(int x, int y) : self
{
	new Self
	{
		.x = x,
		.y = y,
		.z = 2
	}
}
{}

Pimpl::~Pimpl() noexcept
{
	delete self;
}

auto Pimpl::operator = (Pimpl const& other) -> Pimpl&
{
	delete self;
	       self = new Self { *other.self };

	return *this;
}

auto Pimpl::operator = (Pimpl&& other) noexcept -> Pimpl&
{
	delete self;
	       self = other.self;
	              other.self = nullptr;

	return *this;
}

auto Pimpl::x() const noexcept -> int
{
	return self->x;
}

auto Pimpl::y(int y) noexcept -> Pimpl&
{
	self->y = y;
	self->setZ(self, &y);

	return *this;
}

auto Pimpl::z(int z) const noexcept -> int
{
	return self->getZ(self, z < 0 ? -z : z);
}

/////////////////////////////////////////////////////////////////////////////
```
