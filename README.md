# Synopsis

This repository is an Elixir learning exercice.

**Disclaimer : Do not use this code in production.**

## Subject of the exercice

The subject of this exercice is to play with binary and pattern matching.

`CryptoBlocks` is used to split a binary in many blocks of specific size.

The input binary can be passed in one time to `CryptoBlocks` or in many times with chunks of different size. (ex: receiving a big file from socket and reading the stream with a buffer)

As we are working with an accumulator to fit the size of each block to the required size it is necessary to call the `final()` function at the end of the processus.

# Usage

Create a `CryptoBlocks` structure with initial values :

- **storage** : The absolute path where to write the blocks
- **size** : The size of each block in bytes

### Example : Reading an entire file and split the binary

```elixir
{ :ok, file } = File.open filepath, [:read, :binary]
data = IO.binread file, :all
File.close file

blocks = %CryptoBlocks{storage: "/..path...", size: 256}
  |> CryptoBlocks.write(data)
  |> CryptoBlocks.final()

IO.inspect blocks
```

### Example : Reading many chunks

_(Each chunks can be of different size)_

```elixir
s = %CryptoBlocks{storage: "/..path...", size: 256}

# ... read the first chunk (from a socket or a stream)

s1 = CryptoBlocks.write(s, chunk1)

# ... read the second chunk

s2 = CryptoBlocks.write(s1, chunk2)

# ... read the next chunk

s3 = CryptoBlocks.write(s2, chunk3)

# Call the `final()` function and get the blocks description

blocks = CryptoBlocks.final(s3)

IO.inspect blocks
```

or

```elixir
blocks = %CryptoBlocks{storage: "/..path...", size: 256}
  |> CryptoBlocks.write(chunk1)
  |> CryptoBlocks.write(chunk2)
  |> CryptoBlocks.write(chunk3)
  |> CryptoBlocks.final()
```

## The blocks description

After calling the `final()` function you will receive a list containing the description of each block.


```
[
	%{                  # block 1
	  id: << ...>>,     # id is used as file name for the block
	  md5: << ...>>     # block md5
	},
	%{                  # block 2
	  id: << ...>>,
	  md5: << ...>>
	},
	...
]
```

Usually the last block is the remaining accumulator and it is smaller than the other blocks.
_(the last block will have the same size than the other blocks only when the input binary size is a multiple of the choosen output block size)._

## Rebuild the original binary

- **blocks** : the blocks description
- **storage** : the absolute path where are stored the blocks
- **dest** : where to write the binary

```Elixir
dest = "/absolute/path/myfile.txt"
CryptoBlocks.rebuild(blocks, storage, dest)
```

# Status

- [x] Basic file splitter
- [x] Basic tests
- [ ] Add block encryption
- [ ] Error handling
- [ ] Add specs
- [ ] Add examples usage
- [ ] Add documentation

# Author

Author : @odelbos