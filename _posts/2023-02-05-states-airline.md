---
layout: post
title:  "StatesAirline"
date:   2023-02-05
author: hyobbeee
categories: JavaScript
tags: TIL JavaScript
cover:  "/assets/instacode.png"
---

# Part 1: 항공권 목록 필터링

![111](https://user-images.githubusercontent.com/99409757/216825221-d6d2ad9c-56c2-4718-b338-4d037f9c3f75.png)

첫번째 테스트는 state & props를 활용한 useState 구문으로 해결 가능하고,

두번째 테스트는 SideEffect(함수 내에서 어떤 구현이 외부함수에 영향을 끼치는 경우)를 UseEffect 구문으로 해결할 수 있다.

### `UseEffect` 활용법

1. 빈배열 넣기 : `useEffect(함수, [])`  → [CSR](https://www.notion.so/0130-HTTP-ea6817eca3d34fd3b4572f20b88659cc) 사용시 사용
    
    이 경우에는 컴포넌트가 처음 생성될 떄만 effect 함수가 실행된다. → 컴포넌트 생성시 1회 실행
    
    - 필요한 경우
        
        대표적으로 처음 단 한 번, 외부 API를 통해 리소스를 받아오고 더이상 API 호출이 필요하지 않을 때 사용 가능하다. → AJAX(SPA 방식) 사용시에 적용 가능
        
2. 아무것도 넣지 않기 (기본 형태) : `useEffect(함수)` → SSR 사용시 사용
    
    기본 형태의 useEffect는 state가 업데이트 될 때마다 실행된다. → 다중 실행
    

(여기서 ajax가 기억이 잘 안나서 다시 정리)

### 🚨 ajax

ajax의 가장 큰 특징은 웹페이지에 필요한 부분에 필요한 데이터만 비동기적으로 받아와 화면에 그려낼 수 있다는 것이다.

브라우저는 Fetch가 서버에 요청을 보내고 응답을 받을때까지 모든 동작을 멈추는 것이 아니라, 계속해서 페이지를 사용할 수 있게 만들어 준다.

---

## ✔️Main.js

메인 (부모컴포넌트)에 있는 search함수가search 컴포넌트 (자식컴포넌트)의 검색 버튼 클릭시 실행되어야 한다 - 상태끌어올리기를 사용하자

```jsx
import Head from 'next/head';
import { useEffect, useState } from 'react';
import { getFlight } from '../api/FlightDataApi';
import FlightList from './component/FlightList';
import LoadingIndicator from './component/LoadingIndicator';
import Search from './component/Search';
import Debug from './component/Debug';
// 후반 테스트를 진행할 때 아래 import를 삭제합니다.
// fligthList에서 json으로 import 했던것을 주석처리 했는데도 동작해서 테스트 코드가 통과가 안됐었다.

export default function Main() {
  // 항공편 검색 조건을 담고 있는 상태 (condition은 상태 변수이다)
  const [condition, setCondition] = useState({
    departure: 'ICN',
  });
  const [flightList, setFlightList] = useState([]); // useState(json)은 초기값일 뿐이다. 
  const [isLoading, setIsLoading] = useState(false); // 로딩 상태 설정, 초기값은 false (isLoading = false -> 초기에는 로딩중이 않음)

  // 주어진 검색 키워드에 따라 condition 상태를 변경시켜주는 함수
  const search = ({ departure, destination }) => {
    if (
      condition.departure !== departure || 
      condition.destination !== destination
    ) {
      // TODO: search 함수가 전달 받아온 '항공편 검색 조건' 인자를 condition 상태에 적절하게 담아보세요.
      setCondition({departure, destination}); // 상태를 변경시켜야 하므로 search의 인자를 받아 condition을 변경시킨다.
    }
  };
  // TODO: Effeck Hook을 이용해 AJAX 요청을 보내보세요.
  // TODO: 더불어, 네트워크 요청이 진행됨을 보여주는 로딩 컴포넌트(<LoadingIndicator/>)를 제공해보세요제
  // useEffect로 상태를 변화하는 condition을 FlightDataApi의 getflight로 넘겨준다

  // useEffect(async() => {
  //   setIsLoading(true);
  //   setFlightList(await getFlight(condition));
  //   setIsLoading(false);
  // }, [condition])
  // 검색 조건이 바뀔 때마다 useEffect 실행

  useEffect(() => { // 외부에서 호출한 값(node.js 환경)으로 내부 상태값(react 환경)을 변경시켜야 하니 side effect로 볼 수 있다. (로컬 함수의 제공값을 이용하는게 아니라 서버의 데이터를 이용함) side effect는 effect hook으로 처리
    setIsLoading(true); // useEffect가 호출되면 데이터를 받아와야 하니 false 였던 isLoading 상태를 true로 변경시켜준다.
    getFlight(condition) // import한 getFlight에서 condition 객체로 필터링을 진행하여 데이터를 추출한 후
    .then((filtered) => { // 그 데이터(departure, destination)를 이용해 FlightList와 isLoading의 상태를 변경시켜준다.
      setFlightList(filtered); // flightList에는 받아온 데이터를 넘겨주어 상태를 변경시켜주고
      setIsLoading(false); // 데이터를 받아왔으니 로딩화면도 종료시켜준다.
    });
  }, [condition]); // useEffect의 두번째 인자 [condition]이 변경될 때마다 앞의 콜백 함수가 실행됨. ([]는 컴포넌트 생성시에만 실행됨)

  // 이전에 사용한 필터 함수 삭제
  // const filterByCondition = (flight) => {
  //   let pass = true;
  //   if (condition.departure) {
  //     pass = pass && flight.departure === condition.departure;
  //   }
  //   if (condition.destination) {
  //     pass = pass && flight.destination === condition.destination;
  //   }
  //   return pass;
  // };

  global.search = search; // 테스트 코드

  // TODO: 테스트 케이스의 지시에 따라 search 함수를 Search 컴포넌트로 내려주세요.
  return (
    <div>
      <Head>
        <title>States Airline</title>
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <main>
        <h1>여행가고 싶을 땐, States Airline</h1>
        <Search onSearch={search} /> 
        {/* Search 컴포넌트에서는 상태변경 함수인 search를 onSearch props로 전달 되어야 한다. */}
        <div className="table">
          <div className="row-header">
            <div className="col">출발</div>
            <div className="col">도착</div>
            <div className="col">출발 시각</div>
            <div className="col">도착 시각</div>
            <div className="col"></div>
          </div>
          {/* <FlightList list={flightList.filter(filterByCondition)} /> */}
          {isLoading ? <LoadingIndicator /> : <FlightList list={flightList} />} 
          {/* 로딩 중이면 LoadingIndicator, 아니면 FlightList의 list 항목 추출 */}
        </div>

        <div className="debug-area">
          <Debug condition={condition} />
        </div>
        <img id="logo" alt="logo" src="codestates-logo.png" />
      </main>
    </div>
  );
}
```

## ✔️Search.js

```jsx
import { useState } from 'react';

function Search({onSearch}) { // 상태변경 함수 'search'가 'onSearch' props로 전달되어야 한다. 함수로 받기때문에 { }를 적어줘야한다.
  const [textDestination, setTextDestination] = useState('');

  const handleChange = (e) => { // 받아온 인자값 대문자로 만듦
    setTextDestination(e.target.value.toUpperCase());
  };

  const handleKeyPress = (e) => { // 엔터 버튼이 눌렸는지 감지하는 이벤트
    if (e.type === 'keypress' && e.code === 'Enter') { // 키가 눌렸고, 그게 Enter라면 아래 함수를 실행시킴
      handleSearchClick();
    }
  };

  const handleSearchClick = () => {
    const departure = "ICN";
    const destination = textDestination === "" ? null : textDestination;
    console.log('검색 버튼을 누르거나, 엔터를 치면 search 함수가 실행됩니다');
    // TODO: 지시에 따라 상위 컴포넌트에서 props를 받아서 실행시켜 보세요.
    // search는 setCondition({departure, destination})을 실행
    // 검색 버튼 클릭시 -> onClick -> {handleSearchClick}
    onSearch({departure, destination});
  };

  return (
    <fieldset>
      <legend>공항 코드를 입력하고, 검색하세요</legend>
      <span>출발지</span>
      <input id="input-departure" type="text" disabled value="ICN"></input>
      <span>도착지</span> {/* 도착지 입력칸 -> 이벤트 함수 3가지 ( handleSearchClick, handleChange, handleKeyPress) */}
      <input
        id="input-destination"
        type="text"
        value={textDestination}
        onChange={handleChange}
        placeholder="CJU, BKK, PUS 중 하나를 입력하세요"
        onKeyPress={handleKeyPress}
      /> 
      <button id="search-btn" onClick={handleSearchClick}>
        검색
      </button>
    </fieldset>
  );
}

export default Search;
```

# Part 2: AJAX 요청

## ✔️FlightDataApi.js

FlightDataApi에서 기존 구현 대신, REST API를 호출하도록 바꿉니다

```jsx
import flightList from '../resource/flightList';
import fetch from 'node-fetch';

// if (typeof window !== 'undefined') {
//   localStorage.setItem('flight', JSON.stringify(flightList));
// }

export function getFlight(filterBy = {}) {
  // HINT: 가장 마지막 테스트를 통과하기 위해, fetch를 이용합니다. 아래 구현은 완전히 삭제되어도 상관없습니다.
  // TODO: 아래 구현을 REST API 호출로 대체하세요.

  let queryString = ''; // queryString 변수 선언
  if (filterBy.departure) {  // filterBy.departure가 호출되면 즉 export한 getFligth이 호출되면 (Main 컴포넌트 참고 -- getFlight(condition))
    queryString = queryString + `departure=${filterBy.departure}&` // queryString 에 필터링 된 departure를 json화 시켜서 저장
  } 
  if (filterBy.destination) { // filterBy.destination이 호출되면 즉 export한 getFlight이 호출되면 (Main 컴포넌트 참고 -- getFlight(condition))
    queryString = queryString + `destination=${filterBy.destination}` // queryString에 필터링된 destination도 json화 시켜 저장
  }
  let endpoint = `http://ec2-13-124-90-231.ap-northeast-2.compute.amazonaws.com:81/flight?${queryString}`; // 필터링된 departure와 destination의 값을 담은 queryString을 명시한 url 설정

  return fetch(endpoint)  // url을 서버에 요청한다.
    .then(res => res.json()); // 받아온 데이터를 json화 시켜 저장한다.

/* 지연 요청 테스트용 코드
  let json = [];
  if (typeof window !== 'undefined') {
    json = localStorage.getItem('flight');
  }
  const flight = JSON.parse(json) || [];

  return new Promise((resolve) => {
    const filtered = flight.filter((flight) => {
      let condition = true;
      if (filterBy.departure) {
        condition = condition && flight.departure === filterBy.departure;
      }
      if (filterBy.destination) {
        condition = condition && flight.destination === filterBy.destination;
      }
      return condition;
    });

    setTimeout(() => {
      resolve(filtered);
    }, 500);
  });
}
*/
}
```