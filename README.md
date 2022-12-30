# Hadal Zone : 제3의 눈 
**서울문화재단 청년예술청 지원사업 - <스페이스랩:아직> 선정작품** **, AR 개발자로 참여**
<img width="806" alt="image" src="https://user-images.githubusercontent.com/59603575/102004337-9cfcc580-3cc4-11eb-93ce-d2bdaba81a20.png">

---

## 프로젝트 개요 : AR + 뇌파센서를 이용한 전시물
**특징** 
- 뇌파센서에서 받은 값에 따라 설치한 AR Object의 형태, 색, 투명도 변화
- NeuroSky의 mindwave2 사용

<img width="924" alt="image" src="https://user-images.githubusercontent.com/59603575/102004364-e3522480-3cc4-11eb-9e56-e2dab60f9fe0.png">

---

## UML

<img width="1161" alt="image" src="https://user-images.githubusercontent.com/59603575/102004406-375d0900-3cc5-11eb-9054-e39ae4bdebff.png">

---

## 개발내용

### AR 환경 구성

#### MaterialMgr일부

```c#
void Update()
{
    CalculateValue();
    ChangeIntensity();
}

void CalculateValue()
{
    attenstionVal = (float)((float)DisplayData.Instance.Attention / 100.0f);
    meditationVal = (float)((float)DisplayData.Instance.Meditation / 100.0f);
}

void ChangeIntensity()
{
    bigBall.SetFloat("_intensity", attenstionVal * 2f);
    smallBall.SetFloat("_intensity", attenstionVal * 1.5f);
    seaFood.SetFloat("_intensity", attenstionVal * 3f);
}
```

- TouchMgr안에 들어있는 placeObject prefab의 스크립트
- 만들어졌을때 DisplayData의 Attention값을 가져와 값에 따라 변화

#### DisplayData일부

```c#
void OnGUI"(){
  if(GUI.Button( new Rect(190,20,100,80),"Init")){
    UnityThinkGear.Init(true);
  }

  if(GUI.Button(new Rect(190,140,100,80),"Connect")){
    print("Connect Button CLick");
    #if UNITY_IPHONE
    clearDataArr();
    UnityThinkGear.ScanDevice();
    showListViewFlag = true;

    #elif UNITY_ANDROID
    UnityThinkGear.StartStream();
    #endif
  }

  if(GUI.Button(new Rect(190,250,100,80),"Quit")){
    Application.Quit();
  }

  if (GUI.Button(new Rect(190, 360, 100, 80), "PlaceInit")){
      TouchMgr.Instance.isTouch = false;
  }
}
```
- Init button: UnityThinkGear.init() 실행
- Connect button : 뇌파센서와 블루투스 연결시도
- Quit button : 어플리케이션을 끔
- PlaceInit button : 지금 있는 object를 없애고 다시 생성하기 전 상태로 만듬

#### TouchMgr일부


```c#
if (isTouch == false)
{
    if (Input.touchCount == 0)
        return;

    Touch touch = Input.GetTouch(0);

    TrackableHit hit;

    TrackableHitFlags raycastFilter = TrackableHitFlags.PlaneWithinPolygon | TrackableHitFlags.FeaturePointWithSurfaceNormal;

    if (touch.phase == TouchPhase.Began
        && Frame.Raycast(touch.position.x
                        , touch.position.y
                        , raycastFilter
                        , out hit))
    {
        var anchor = hit.Trackable.CreateAnchor(hit.Pose);

        GameObject obj = Instantiate(placeObject
                                    , hit.Pose.position
                                    , Quaternion.identity
                                    , anchor.transform);

        var rot = Quaternion.LookRotation(ARCamera.transform.position
                                            - hit.Pose.position);

        obj.transform.rotation = Quaternion.Euler(ARCamera.transform.position.x
                                                    , rot.eulerAngles.y
                                                    , ARCamera.transform.position.z);

        isTouch = true;
    }
}
```
- 바닥이 인식된 지점에 터치를 할때 그 자리에 object가 생성
- 이미 object가 있을 경우 생기지 않음

---
## 전시 사진

<img width="920" alt="image" src="https://user-images.githubusercontent.com/59603575/102004581-d9312580-3cc6-11eb-845b-8a213bdf425b.png">


---

## 사용 기술
- Unity 2018.4.1f
- NeuroSky Mobile 2
- AR Core
