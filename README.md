# kakao_api
```python
import numpy as np
import requests


```

## 주소 변환
- 카카오 api(https://developers.kakao.com/docs/latest/ko/local/dev-guide) 활용
    - 주소 검색 url : https://dapi.kakao.com/v2/local/search/address.json
    - 키워드 검색 url : https://dapi.kakao.com/v2/local/search/keyword.json
    - 좌표 -> 주소 변환 url : https://dapi.kakao.com/v2/local/geo/coord2address.json


```python
def kakaoAddress(my_token, address):
    '''
    input 
     - adress : 검색 주소
    output 
     - 시명, 구명, 동명, 위도, 경도가 들어있는 딕셔너리
    '''
    url = "https://dapi.kakao.com/v2/local/search/address.json"

    # method = 'GET'
    params = {'query' : address}
    header = {'authorization' : my_token}

    response = requests.get(url, headers=header, params=params)
    tokens = response.json()
#     print(tokens)
    
    document = {}
    if tokens['documents'] != []:   # 카카오_주소검색 api 호출 결과 있을 경우 

        if tokens['documents'][0]['address'] != None:
            address_info = tokens['documents'][0]['address']
        else :
            address_info = tokens['documents'][0]['road_address']

        document['시명'] = address_info['region_1depth_name']
        document['구명'] = address_info['region_2depth_name']
        document['동명'] = address_info['region_3depth_name']
        document['위도'] = address_info['y']
        document['경도'] = address_info['x']

    return document
```


```python
def kakaoKeyword(my_token, address):
    url_keyword = "https://dapi.kakao.com/v2/local/search/keyword.json"
    url_coord = "https://dapi.kakao.com/v2/local/geo/coord2address.json"
    
    
    header = {'authorization' : my_token}
    params = {'query' : address}
    
    response = requests.get(url_keyword, headers=header, params=params)
    tokens = response.json()
    
    document = {} 
    
    if tokens['documents'] != []:   # 카카오_키워드검색 api 호출 결과 있을 경우 
        lat = 0
        lng = 0
        
        for token in tokens['documents']:
            if '주거시설' in token['category_name']:
                lat = token['y']
                lng = token['x']
                break
            else : 
                pass

        if lat == 0:
            lat = tokens['documents'][0]['y']
            lng = tokens['documents'][0]['x'] 
        
        params = { 'y' : lat, 'x':lng }
        response = requests.get(url_coord, headers=header, params=params)
        tokens = response.json()
#         print(token)
        address_info = tokens['documents'][0]['address']
#         print(address_info)
        try: 
            document['시명'] = address_info['region_1depth_name']
            document['구명'] = address_info['region_2depth_name']
            document['동명'] = address_info['region_3depth_name'] 
        except :
            pass
        document['위도'] = lat
        document['경도'] = lng    
    
    return document
```


```python
kakaoKeyword(my_token, '서울역')
```




    {'시명': '서울',
     '구명': '중구',
     '동명': '만리동2가',
     '위도': '37.554724533455605',
     '경도': '126.9632574928214'}



