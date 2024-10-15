# nrf-peer-manager-whitelist-bonding- 개념정리 및 상대 디바이스가 random peer address에 대한 IRK키 추가 


## 1. Peer Manager
Peer Manager는 nRF5 SDK에서 페어링 및 본딩과 관련된 모든 데이터를 관리하고 저장하는 모듈입니다. 이 모듈은 BLE 연결 시 페어링된 장치들의 보안 정보를 관리하는 데 중추적인 역할을 합니다.  

Peer Manager의 주요 기능:  
```
Bonding (본딩): 페어링된 장치의 보안 정보를 저장하여, 다음 연결 시에 페어링 과정을 생략하고 바로 연결할 수 있게 합니다.  
Whitelist 관리: 본딩된 장치들의 주소를 사용하여 화이트리스트에 장치들을 추가하거나 관리할 수 있습니다.  
보안 관리: 페어링된 장치들과의 보안 연결을 관리하며, 페어링된 장치들의 암호화 키, ID 키 등을 관리합니다.  
페어링 정보 삭제: 저장된 본딩 정보를 삭제하거나 특정 장치와의 페어링을 해제할 수 있습니다.   
즉, Peer Manager는 페어링 및 본딩 정보 전체를 관리하고, BLE 보안의 중심 역할을 합니다.  
```
## 2. Whitelist  
Whitelist는 BLE 장치가 advertising를 시작하거나 연결 요청을 받을 때, 연결을 허용할 장치들의 리스트입니다. Whitelist에 등록된 장치들만 해당 BLE 장치와 연결을 시도할 수 있습니다. 일반적으로 이미 페어링된 장치들만 다시 연결할 수 있도록 화이트리스트를 설정합니다.  

Whitelist의 주요 특징:  
```
장치 필터링: 화이트리스트에 등록된 장치들만 advertising를 볼 수 있거나 연결 요청을 보낼 수 있습니다.   
보안 향상: 지정된 장치들만 다시 연결할 수 있도록 제한함으로써 보안을 강화할 수 있습니다.  
advertising 필터링: BLE advertising 시 ble_gap_adv_params_t 구조체의 filter_policy 필드를 사용하여, advertising와 스캔 요청을 화이트리스트에 등록된 장치들만 볼 수 있게 할 수 있습니다.  
Whitelist는 Peer Manager에 의해 관리될 수 있으며, 본딩된 장치들만 화이트리스트에 추가할 수 있습니다.  
```

## 3. Bonds (본딩)  
**Bonding(본딩)**은 BLE에서 두 장치가 페어링을 완료한 후, 페어링 정보를 영구적으로 저장하는 과정입니다. 본딩이 이루어지면, 이후 연결할 때마다 다시 페어링 과정을 거치지 않고 바로 연결할 수 있습니다.  

Bonding의 주요 특징:  
```
보안 정보 저장: 장치 간 교환된 암호화 키, ID 정보, 보안 키 등은 본딩 후에 영구적으로 저장됩니다.   
빠른 재연결: 본딩된 장치는 이후 연결 시에 보안 정보를 재사용하여 페어링 없이 빠르게 연결됩니다.
페어링과 본딩의 차이: 페어링은 두 장치가 보안 키를 교환하는 절차이고, 본딩은 이 정보를 저장하는 단계입니다.
```
차이점 요약  
개념	설명	주요 역할  
```
Peer Manager	페어링 및 본딩 정보를 관리하고 저장하는 SDK 모듈입니다.	본딩 정보, 페어링 데이터 관리, 보안 관리
Whitelist	advertising 또는 연결 요청을 받을 장치를 제한하는 리스트입니다. 페어링된 장치들만 연결하도록 제한할 때 주로 사용됩니다.	advertising/연결 필터링, 보안 강화 (특정 장치들만 연결 허용)
Bonds	페어링 정보를 영구적으로 저장하여 이후 연결에서 보안 정보를 재사용할 수 있도록 합니다.	보안 정보 저장, 빠른 재연결 (페어링 과정을 생략하고 바로 연결 가능)
관계
```
```
Peer Manager는 본딩된 장치들의 정보를 관리하며, 이 정보를 기반으로 Whitelist에 추가할 수 있습니다.
Bonding은 페어링된 장치의 보안 정보를 저장하는 과정이며, Peer Manager에 의해 관리됩니다.
Whitelist는 본딩된 장치들만 다시 연결을 허용할 때 자주 사용되며, Peer Manager에 의해 관리될 수 있습니다.
이러한 개념들은 BLE에서 보안 및 장치 관리의 핵심 요소로 작동하며, 서로 유기적으로 연결되어 BLE 연결의 안정성과 보안을 강화합니다.
```



## android 기기에서 peer manager

android 기기에서 peer address 값은 장치에 연결될 때마다 계속 변합니다. (보안)  
그렇기 때문에 Peer Manager가 IRK를 사용해 변하는 주소를 해석하고, Peer ID를 기반으로 장치를 식별합니다. Peer Address는 변하지만, Peer ID는 변하지 않습니다.
```
IRK (Identity Resolving Key)를 사용하여 Peer Manager에서 **Resolvable Private Address (RPA)**를 통해 변하는 주소를 해석할 수 있기 때문에, 주소 변경에 관계없이 동일한 장치임을 확인할 수 있습니다.

Peer Address 자체는 RPA로 인해 변할 수 있지만, Peer Manager가 이를 IRK를 사용하여 올바르게 처리하면 장치 식별에 문제가 없습니다.

Peer Manager는 Bonding된 장치의 IRK를 사용하여 변하는 주소를 해석해, 변동하는 주소와 상관없이 동일한 장치로 인식합니다.
```

======
## peer address 와 peer id의 차이 
1. Peer Address:  
Peer Address는 BLE 장치의 물리적 주소로, 주로 BLE 연결을 설정할 때 사용됩니다.  
BLE 장치는 두 가지 유형의 주소 체계를 가질 수 있습니다:  
Public Address: 하드웨어적으로 고정된, 장치 제조 시 부여된 고유 주소입니다.  
Random Address: 장치가 임시로 생성한 주소로, **Resolvable Private Address (RPA)**와 Non-Resolvable Random Address가 있습니다.  
**Resolvable Private Address (RPA)**는 장치가 주기적으로 주소를 변경하는데, 이 주소는 IRK (Identity Resolving Key)를 사용해 해결할 수 있습니다.  
Peer Address는 BLE 연결을 시도할 때 사용되는 기본적인 식별자입니다. 하지만 장치가 랜덤 주소를 사용하는 경우, 연결 시마다 주소가 변경될 수 있습니다.  





2. Peer ID:  
Peer ID는 Nordic의 Peer Manager에서 관리하는 장치의 논리적 ID입니다.  
Peer ID는 BLE 장치가 Bonding(페어링)될 때 생성되며, 이후에 연결할 때 그 장치가 이전에 Bonding된 장치임을 확인하는 데 사용됩니다.  
Bonding이 성공한 장치는 Peer Manager에 Persistent Storage에 기록되며, 동일한 장치는 이후 연결할 때 항상 같은 Peer ID를 갖게 됩니다. Peer ID는 물리적 주소(Peer Address)와는 관계없이 Bonding된 장치의 영구 식별자 역할을 합니다.  
Peer ID는 장치의 **보안 정보(예: 암호화 키, IRK 등)**를 관리하고, 이를 통해 이전에 Bonding된 장치와의 재연결 시 동일한 장치임을 확인하는 데 사용됩니다.  
  
