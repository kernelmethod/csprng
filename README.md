# PyTorch/CSPRNG

**IMPORTANT NOTE:** this is a fork of the original
[pytorch/csprng](https://github.com/pytorch/csprng) repo with some new useful
features (in particular, reproducible CSPRNG).

This library **has not** received an audit, and you should think carefully
before using it in a production system.

## Installation

This fork of `torchcsprng` requires [libsodium](https://doc.libsodium.org/) to
work correctly; you should install it before installing this library.

Once you have libsodium installed, run:

```
pip install git+https://github.com/kernelmethod/csprng.git@main
```

Alternatively, add the following to your `requirements.txt`:

```
torchcsprng @ git+https://github.com/kernelmethod/csprng.git@main
```

## Usage

This library exports one function, `create_generator`, which creates a new
PyTorch `Generator` object with cryptographically secure pseudo-random number
generation (CSPRNG).

```python
>>> import torchcsprng as csprng
>>> gen = csprng.create_generator()
```

Note that the current implementation uses [JIT-compiled
extensions](https://pytorch.org/tutorials/advanced/cpp_extension.html#jit-compiling-extensions),
so it may take a moment to compile the module the first time that you call it.

Once you've created your `Generator` instance, you can feed it to PyTorch's RNG
functions, e.g.

```python
>>> import torch
>>> torch.randn(3, 3, generator=gen)
tensor([[ 0.5859, -0.6674, -0.2723],
        [ 0.9075,  2.0364,  0.0331],
        [-0.2692, -1.1712, -1.5100]])
```

When you call `create_generator` without any arguments, it will automatically
generate an secret key from the operating system's random stream. You can
explicitly generate a key and pass it in to PyTorch in order to make RNG
deterministic:

```python
>>> import secrets
>>> key_bytes = secrets.token_bytes(32)
>>> key = torch.tensor([b for b in key_bytes], dtype=torch.uint8)
>>> gen = csprng.create_generator(key)
```
