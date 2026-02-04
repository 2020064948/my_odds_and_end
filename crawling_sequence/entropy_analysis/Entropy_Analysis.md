엔트로피 계산식은 이미지 분석에 널리 쓰이는 섀넌 엔트로피 계산식을 이용하려고 한다.
이 때 OpenCV를 이용하는 방법과 직접 픽셀데이터를 추출하여 분석하는 방법이 있다.

OpenCV를 이용한다면 Webp에 한정되지 않고 여러 포맷의 이미지들을 분석하도록 확장시킬 수 있다.
+또한 그레이스케일 변환, 히스토그램 평활화, 노이즈 제거 등 OpenCV의 분석 함수들을 이용할 수 있다.
-다만 그레이스케일 변환 과정에서 하위 비트의 숨겨진 데이터 패턴이 뭉개질 가능성이 있다.
(LSB 왜곡 문제는 OpenCV에서 채널 분리하고 cvtColor같은 연산 없이 각 채널 별로 엔트로피 계산을 실행한다면 회피 가능)

직접 픽셀 데이터를 추출하고 분석한다면 
+OpenCV라는 거대한 라이브러리 전체를 로드할 필요 없이 libwebp만 연결하면 되기에 바이너리 크기가 작아진다.
-다만 PNG나 JPG같은 이미지 포맷을 다루려면 새로 디코딩 로직을 작성해야 하고, 이미지 회전, 크기 조절 등의 전처리를 직접 구현해야 한다.

다만 지금은 WebP 포맷에 한정된 상황이므로
#include <vector>
#include <cmath>
#include <webp/decode.h>

double calculateEntropyFromRaw(const std::vector<unsigned char>& pixels) {
    std::vector<long> counts(256, 0);
    for (unsigned char p : pixels) counts[p]++; // 빈도수 카운트

    double entropy = 0;
    double totalSize = pixels.size();

    for (int i = 0; i < 256; i++) {
        if (counts[i] > 0) {
            double p = (double)counts[i] / totalSize;
            entropy -= p * log2(p);
        }
    }
    return entropy;
}

int width, height;
uint8_t* rgb_data = WebPDecodeRGB(buffer, data_size, &width, &height);

WebPFree(rgb_data);

와 같이 직접 픽셀 데이터를 추출하고 간단하게 분석하기로 한다.
만약 시간을 들인다면 채널별 분리 분석, LSB 추출, 슬라이딩 윈도우 기법등을 활용해 
다양한 방식으로 분석하는 것도 좋을 것이다.
