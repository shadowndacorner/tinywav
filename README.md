# tiny-wav
A small, header-only C library to load wav files.

## Building
Add tiny_wav/include to your build system.  If you use cmake, you can add this as a subdirectory and link against tinywav, which will add the include directory automatically.  If you are going this route, be sure to `set(TW_WITH_SAMPLE off)`.

## Usage
```C
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
```

## Limitations
Currently only supports PCM formatted WAV files