WebP 컨테이너 내부의 청크를 탐색해 비정상적인 크기나 알 수 없는 메타데이터가 삽입되었는지 여부를 감지하기 위해선 WebP의 청크가 어떤 구조를 형성하는 지를 알아야 한다.

WebP는 RIFF(Resource Interchange File Format) 컨테이너 구조를 따르며, 전체 파일은 여러 개의 청크로 구성된다.
각 청크는 다음 정보를 포함한다.
    Chunk Type (FourCC)
    Chunk Size
    Chunk Data

Structural analysis 단계에서 검증해야할 것들은 다음과 같다.
    필수 청크의 존재 여부
    청크 순서가 RIFF에 부합하는지 여부
    중복이 허용되지 않는 청크의 반복 여부
    Chunck Size와 실제 데이터 크기의 일치 여부
    비정상적으로 큰 메타데이터 청크의 존재여부
    전체 파일 끝을 초과하는 Chunk Size
    RIFF header에 명시된 전체 데이터 크기와 실제 파일 크기의 비교
    각 Chunck Size의 누적 합
    Chunk 개수가 비정상적으로 많은지 여부
    의미없는 구조의 반복 여부
    이미지 데이터와 무관한 영역이 과도하게 존재하는 지의 여부
검색해서 찾아보니 참 많다.
