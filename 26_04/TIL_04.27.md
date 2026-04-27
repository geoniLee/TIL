# TIL 4.27
<h3>알고리즘 문제 풀이</h3>
<h4>양과 늑대</h4>

* 알고리즘
    * DFS
    * 백트래킹
    
* 아이디어
    1. 양의수, 늑대의 수, 진행 가능한 노드를 가지고 다음 상태로 진입
    2. 후보지를 하나씩 이동해보며 이동 가능한지 확인
        1. 이동 했을 때 늑대의 수보다 양의 수가 같거나 적다면 그 후보지로의 이동은 고려하지 않음
        2. 양의 수가 많다면 이번에 이동한 경로를 후보지에서 제외하고, 현재 위치에서 이동가능한 곳을 후보지로 추가하여 이동 후 탐색
        3. 1로 초기화된 maxS 와 비교하여 이동할 수 있는 후보지 중 가장 큰 값을 반환

---
<h3>Cpp과 Unreal Engine으로 3D 게임 개발 </h3>
<h4>캐릭터 체력 및 점수 관리 시스템 구현하기</h4>

* PlayerState

    각 플레이어 마다의 정보를 구현해주는 클래스
    - 주로 멀티 플레이 환경에서 각 플레이어간 데이터 동기화를 위해 사용(점수 / 킬, 데스 카운트)

* 데미지 처리
    * UGamePlayStatic::ApplyDamage()
        * 공격자가 데미지를 줄 대상 액터와 데미지 양, 데미지를 유발한 주체을 인자로 넘겨 호출
        * 내부적으로 액터의 TakeDamage()함수를 호출하려 시도
    * AActor::TakeDamage() - 데미지를 받는 것
        * Actor의 가상함수
        * 체력 감소 / 특수한 데미지 처리 로직을 구현
        ```
        virtual float TakeDamage(float DamageAmount, struct FDamgeEvent const& DamageEvent, AController* EventInstigator, AActor* DamageCauser) override;
        ```
        * DamaAmount: 데미지 값
        * DamageEvent: 어떤 방식으로 데미지를 받았는지 추가 정보 
        * EventInstigator: 데미지를 유발한 주체
        * DamageCauser: 데미지를 직접 발생시킨 오브젝트

* GameState

    전역 정보를 저장하는 클래스
    * GameMode와 연동해서 사용

---
<h4>게임 루프 설계를 통한 게임 흐름 제어하기</h4>

* GameInstance
    
    프로젝트가 시작될 때부터 애플리케이션이 완전히 종료될 때 까지 유일하게 계속 살아있는 객체(싱글턴)

* SeamlessTravel

    GameState/Playerontroller등을 파괴하지 않고 그대로 다음 맵으로 넘어가는 기능
    * 멀티 플레이 환경에서 주로 사용
    
---

<h4>UI 위젯 설계와 실시간 데이터 연동하기</h4>

* HUD(Heads-Up Display)

    게임 내에서 플레이어에게 정보를 제공하기 위한 화면
    * Canvas 기반 HUD
        * AHUD 클래스를 상속하여 구현
        * 기본적인 2D 그리기 작업
    * UMG(Unreal Motion Graphics)
        * WBP(Widget Blueprint 기반) 

* WBP
    * 보통 PlayerController에서 구현
    * 생성
        1. CreateWidget\<UUSerWidget>(this, HUDWidgetClass)
        2. HUDWidget->AddtoViewport()

* .bulid.cs

    UBT(언리얼 빌드 툴)이 Cpp 모듈을 어떻게 빌드할지 읽는 설정 파일
    * PublicDependencyModuleNames.AddRange에 "UMG" 추가 하여 위젯 관련 Cpp 코드 사용 가능

* UI 연결
    * 바인딩
        * Tick 함수 사용하여 갱신
    * SetText(Cpp)
        * 필요할 때만 갱신 시점 조정 가능
        * 순서
            1. 컨트롤러 -> 사용하는 컨트롤러 가져오기
            2. Widget 가져오기
            3. 사용할 것 가져오기 현재는 UTextBlock
                ```
                Cast<UTextBlock>(HUDWidget->GetWidgetFromName(TEXT("Txt_Time")))
                ```

            4. SetText(FText::FormString::FString(Printf(TEXT(""))))로 초기화
---

<h4> 게임 흐름에 맞춘 메뉴 UI 구현하기</h4>

* UI 입력
    * SetInputMode(FInputModeUIOnly()): UI에 입력을 받는 모드로 변경
    * bShowMouseCursor = true: 마우스가 보이도록 변경

* 게임 입력
    * SetInputMode(FInputModeGameOnly()): 게임에 입력을 받는 모드로 변경
    * bShowMouseCursor = false: 마우스가 안보이도록 변경

* UI 삭제
    * HUDWidgetInstance->RemoveFromParent()

        UI을 새로 생성하기 전 기존에 있을 수도 있는 UI 제거