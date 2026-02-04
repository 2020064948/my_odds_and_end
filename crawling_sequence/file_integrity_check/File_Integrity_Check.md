File Integrity Check를 위해 Bianry Header를 파싱해서 webp 형식이 맞는지 확인하려고 한다.

WebP 파일은 RIFF(Resource Interchange File Format) 구조를 따르며, 파일의 가장 앞부분 12bytes를 확인하여 형식을 판별한다고 한다.
이때 offset은 각각 0x00, 0x04, 0x08이며, RIFF 파일임을 알리는 식별자, header 8bytes를 제외한 전체 데이터 크기, 실제 데이터가 WebP 형식임을 나타내는 파트로 나뉜다.

정상적인 Webp라면 0x00에서 4bytes는 52, 49, 46, 46 이어야 하고, 0x08에서는 57, 45, 42, 50 이어야 한다. (각각 ASCII로 나타낸 RIFF와 WEBP이다.)

따라서 파일을 바이너리 모드로 열고, 처음 12bytes를 읽어 비교하면 된다. 이후 0x04에서 확인한 파일 크기가 실제 파일 크기와 일치하는지 대조해
데이터 변조 여부를 정적 분석할 수 있다.
