# tiny-wav
A small, header-only C library to load wav files.

## Building
Add tiny_wav/include to your build system.  If you use cmake, you can add this as a subdirectory and link against tinywav, which will add the include directory automatically.  If you are going this route, be sure to `set(TW_WITH_SAMPLE off)`.

Once the header is in your build system, be sure to add
```C
#define TINY_WAV_IMPLEMENTATION
#include <tinywav.h>
```
to a single source file.

## Explanation
All you need to do to use this library is call `tw_load_mem` on a wav file loaded into memory.  It returns a `char*` with the unprocessed wav data loaded in.  You can optionally pass in int pointers to get additional data out of the file, such as sample rate, channels, etc.  Once you are done with the data, you should call tw_free.

You can optionally provide your own memory management functions by defining `TINY_WAV_ALLOC_OVERRIDE` and implementing the functions `void* tw_malloc(size_t)` and `void tw_free(void*)`.  By default, these just wrap malloc and free.  Note that if you are using C++, these functions expect C linkage, so wrap them in `extern "C"`.

## Usage Example (C++)
```C++
// Load file data
std::ifstream stream(filePath, std::ios::binary);
if (!stream.good() || stream.eof())
{
	fprintf(stderr, "Failed to open wav file %s\n", filePath);
	return -1;
}
std::vector<char> m_FileData;

stream.seekg(0, stream.end);
size_t length = stream.tellg();
stream.seekg(0, stream.beg);

m_FileData.reserve(length);
std::for_each(std::istreambuf_iterator<char>(stream), std::istreambuf_iterator<char>(), [&](const char c) {
	m_FileData.push_back(c);
});

// Load wav data from file
int channels;
int samplerate;
int bps;
int alloc_size;
char* data = tw_load_mem(m_FileData.data(), m_FileData.size(), &channels, &samplerate, &bps, &alloc_size);
if (!data)
{
	fprintf(stderr, "TinyWav Error: %s\n", tw_get_error());
	return -1;
}
// do things with the data
tw_free(data);
```

## Limitations
Currently only supports PCM formatted WAV files