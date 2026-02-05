File Integrity Check를 위해 Bianry Header를 파싱해서 webp 형식이 맞는지 확인하려고 한다.

WebP 파일은 RIFF(Resource Interchange File Format) 구조를 따르며, 파일의 가장 앞부분 12bytes를 확인하여 형식을 판별한다고 한다.
이때 offset은 각각 0x00, 0x04, 0x08이며, RIFF 파일임을 알리는 식별자, header 8bytes를 제외한 전체 데이터 크기, 실제 데이터가 WebP 형식임을 나타내는 파트로 나뉜다.

정상적인 Webp라면 0x00에서 4bytes는 52, 49, 46, 46 이어야 하고, 0x08에서는 57, 45, 42, 50 이어야 한다. (각각 ASCII로 나타낸 RIFF와 WEBP이다.)

따라서 파일을 바이너리 모드로 열고, 처음 12bytes를 읽어 비교하면 된다. 이후 0x04에서 확인한 파일 크기가 실제 파일 크기와 일치하는지 대조해
데이터 변조 여부를 정적 분석할 수 있다.

폴리글로트는 binary header에서 RIFF와 WebP로 위장하고 이미지 데이터 끝이나 청크 사이에 다른 포맷의 데이터를 삽입하는 것으로 공격하는데,
이는 파일 크기 대조 결과를 청크 분석 단계(Structural Analysis)에 보내는 것으로 기능 분리를 할 것이다.

실제 코드를 작성하는 단계에서 #pragma pack(n)과 __attribute__((packed))라는 기능에 대해 알게 되었다.
두 코드는 각각 MSVC (windows / visual studio)와 GCC / Clang(linux, macOS)에서 쓰이는 것으로,
padding으로 인한 여백을 제거하고 패킷 혹은 파일의 값들을 정렬하기 위한 것이다.
첫 번째는 아래에 적힌 모든 구조체에 적용되고, 두 번째는 해당 속성을 붙인 구조체에만 적용된다.
