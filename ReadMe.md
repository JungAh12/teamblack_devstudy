team_black study01
==================
Contents
------------------
1. 지도 표시하기
2. 주소 검색하기
3. 마커 생성하기
4. 마커 삭제하기
5. 마커 연결하기

***

### 지도 표시하기

ionic serve -l로 실행 시, 맨처음 실행된다.

    ionViewDidLoad() {
        this.loadmap();
    }

loadmap() 메소드에서 mapOptions과 map 변수 선언하고 Map 인스턴스 생성

    var mapOptions = {
        center: new naver.maps.LatLng(37.3595704, 127.105399),
        zoom: 10
    };

    map = new naver.maps.Map('map', mapOptions);

### 주소 검색하기

     <ion-input type="text" placeholder="Search For Address" [(ngModel)]="place"></ion-input>
     <button item-right ion-button color="secondary" (click)="searchPlace()">
       serach
     </button>

text input을 통해 받은 값을 place로 접근할 수 있게 함. [(ngModel)] 사용.
버튼을 누르면 searchPlace() 호출

    searchPlace() {

        naver.maps.Service.geocode({
            address: this.place         //input으로 받은 주소값
        }, function (status, response) {
            if (status !== naver.maps.Service.Status.OK) {
                return alert('Something wrong!');
            }

            //검색 결과 중 첫번째꺼 적용
            var item = response.result.items[0],
                point = new naver.maps.Point(item.point.x, item.point.y);

            map.setCenter(point);
            //console.log(point);
            marker[2] = new naver.maps.Marker({         //마커 생성
                position: point,                    //검색한 위치에 마커 생성
                map: map
            });
        });
    }

place를 통해 검색할 주소를 받는다.
건물, 상호명은 안된다.
ex) 신촌동 (o), 신촌역(x)

Geocoder 서브모듈은 NAVER 지도 API v3에서 제공하는 서브모듈 중 하나로, 좌표 체계간 변환 메서드와 특정 좌표계를 지원하는 투영객체들을 제공합니다.
출처 : NAVER Maps

geocode()를 이용해 주소를 좌표로 변환해온다.

### 마커 생성하기

마커 생성은 간단함
    marker[2] = new naver.maps.Marker({         //마커 생성
               position: point,                    //검색한 위치에 마커 생성
               map: map
    });

이런식으로 마커를 생성한다.
현재 총 3개, 주소 검색을 통해 표시한 마커, 지도에서 직접 클릭한 마커 두 개 생성할 수 있다.

### 마커 삭제하기

마커 삭제는 delMarker 버튼을 누르면 delMarker() 호출된다.
    for (var i = 0; i < marker.length; i++) {
            if (!marker[i]) {}
            else {
                marker[i].setMap(null);   //마커 사라지게 함.
                delete marker[i];         //마커 삭제
            }
    }

반복문을 통해 마커 3개 중 null이 아닌 것, 즉 마커가 생성된 것만 setMap(null) 메소드로 마커를 사라지게하고 delete 통해 메모리에서 제거한다.

### 마커 연결하기 (=폴리라인 그리기)

start 버튼 누르면 startNav() 호출

    polyline = new naver.maps.Polyline({
        map: map,
        path: [
               new naver.maps.LatLng(marker[0].getPosition().y, marker[0].getPosition().x),
               new naver.maps.LatLng(marker[1].getPosition().y, marker[1].getPosition().x)
            ],
        strokeWeight: 5
    });

polyline 객체 생성해서 그려질 지도 map, 그릴 경로(좌표) path를 넣어주었다.
현재 path는 직접 지도에서 선택한 두 마커의 좌표
marker.getPosition()을 사용해 위도와 경도를 가지고 올 수 있다.
polyline은 직선이기 때문에 결과적으로 직선 경로를 그리게 된다.
polyline은 delMarker로 마커를 삭제할 때 같이 삭제되게끔 delMarker()에서
    polyline.setMap(null);
    polyline = null;
이렇게 삭제시킴.

# 해야할 것 : 경로응답 JSON파일 파싱해서 각 위치를 기점으로 폴리라인 그리기.

참고 사이트

네이버 JavaScript API v3 명세 참고
<https://navermaps.github.io/maps.js/docs/index.html>

네이버 개발자센터에서 검색
<https://developers.naver.com/main/>
