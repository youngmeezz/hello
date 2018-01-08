# hello
#include<stdio.h> //이미지처리하는 필요한 헤더파일 라이브러리 인클루드하라
#include<stdlib.h> // 파일 입출력 stdio.h stdlib.h
#include<Windows.h> // 비트맵파일,인포,팔레트 헤더, 구조체를 가지고있습니다.



void main() {

	BITMAPFILEHEADER hf; // BMP 파일 헤더 14Bytes
	BITMAPINFOHEADER hInfo; // BMP 인포헤더 40Bytes
	RGBQUAD hRGB[256]; // 팔레트(256*4Bytes(알지비쿼드가4바이트9))
	FILE *fp; // 파일로 데이터를 읽거나 내보낼 때 필요 (파일포인터는 주소나 위치를 가리킨다.)
	fp = fopen("lena.bmp", "rb"); //레나 read binary 읽고 파일이 바이너리 형태라서 txt파일이면 rt)
	if (fp == NULL) return; // 레나.bmp가 없을 때 예외처리
	fread(&hf, sizeof(BITMAPFILEHEADER), 1, fp); //레나.bmp 맨 앞에서부터 읽고 메모리 변수에 담음 14byte만큼 읽어서 hf에 담으라
	fread(&hInfo, sizeof(BITMAPINFOHEADER), 1, fp);//40바이트만큼 읽어서 hinfo에다가 담아라
	fread(hRGB, sizeof(RGBQUAD), 256, fp); //4바이트씩 256번씩 읽어서 hRGB라는 배열에 담아라


										   /*영상의 화소정보 읽기*/
										   //영상처리는 동적할당 영상 사이즈 모르니까
										   //Image 배열 원래 영상의 화소 정보를 담을 공간
										   //Ouput 영상 처리 결과
										   //malloc함수 동적할당한 메모리의 첫번째 주소 반환
										   // Image와 Output이라는 포인터변수 Byte로 가리킨다.
	int ImgSize = hInfo.biWidth * hInfo.biHeight; // 영상의 가로사이즈 * 세로사이즈 = 픽셀의 개수 ImgSize크기만큼 동적할당하기위하여 담음
	BYTE * Image = (BYTE *)malloc(ImgSize); // 영상처리 동적할당 하는 코딩 외우기
	BYTE * Output = (BYTE *)malloc(ImgSize);
	fread(Image, sizeof(BYTE), ImgSize, fp);
	//현재 파일 포인터가 가리키고 있는 (파일헤더정보들,인포,팔레트,제일 처음 화소 정보)를 가리키고있고 1바이트 만큼 이미지사이즈 만큼 읽어들임
	fclose(fp);
	//파일에서 모든 정보를 담아가지고 배열에 담아 메모리에 올림 위 4가지 정보담음 파일 관심 xx)
	//fclose함수를 사용하여 파일 포인터와 파일 간의 관계 끊기 이유는 파일을 계속 잡고있음 사용중 상태




	/*영상을 처리하는 과정*/
	//밝으면 255 어두우면 0  
	int val;
	printf("값을 입력:");
	scanf("%d", &val);
	for (int i = 0; i < ImgSize; i++)
		if (Image[i] + val > 255)
			Output[i] = 255;
		else if (Image[i] + val < 0)
			Output[i] = 0;
		else Output[i] = Image[i] + val;



	//블랙이 화이트로 반전됨 
	//Image는 원래 영상
	fp = fopen("output.bmp", "wb"); //파일이 없으면 생성을 해줌 wb wrtie binary
	fwrite(&hf, sizeof(BYTE), sizeof(BITMAPFILEHEADER), fp); //파일로 기록
	fwrite(&hInfo, sizeof(BYTE), sizeof(BITMAPINFOHEADER), fp); //info헤더도 기록하고 

	fwrite(hRGB, sizeof(RGBQUAD), 256, fp); // 팔레트도 기록
	fwrite(Output, sizeof(BYTE), ImgSize, fp); //영상의 화소 정보도 기록
	fclose(fp);
	free(Image); // 동적할당 메모리 해제
	free(Output); // Output 배열에 출력
				  //헤더는 그대로 출력 output배열만 바꿔 출력
				  //메모리 누수현상 leak현상 동적할당했던거 해제하기 fclose함수이용해서
}
