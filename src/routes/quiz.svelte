<script>
	import { goto } from '$app/navigation';
	import { score, quizList } from '$lib/store';
	import { text } from 'svelte/internal';

	// 설명 모드 Or 문제풀기 모드
	let isRunning = false;

	// list 변수에 스토어 quizList 가져오기
	let list = $quizList;

	// 현재퀴즈의 list 순번 저장
	let idx = -1;

	// 현재 퀴즈의 문제와 정답 저장, 최초에는 공백
	let current = null;

	// 현재문재의 진행상태, 보너스 점수
	let bonus = 100;

	// 누적점수는 스토어변수
	$score = 0;

	// 보너스 점수를 제어할 타이머
	let timer;

	// 화면에 영어를 한글로 변환시 필요한 객체
	let kolor = {
		red: '빨강',
		orange: '주황',
		yellow: '노랑',
		purple: '보라',
		green: '초록',
		black: '검정',
		blue: '파랑',
		brown: '갈색',
		gray: '회색',
		pink: '분홍'
	};

	// 시작하기
	function start() {
		list.sort(() => Math.random() - 0.5); // 리스트에 있는 문제 섞기
		alert('준비 됐나요?');
		$score = 0; // 누적 점수 초기화
		next(); // 문제 출제 시작
		isRunning = true;
	}

	function next() {
		idx = idx + 1;
		if (idx === list.length) {
			// 더 이상 문제가 없으면
			stop(); // 멈추기
		} else {
			current = list[idx]; // 리스트에서 새로운 문제 꺼내기
			current.choice.sort(() => Math.random() - 0.5);
			current = current; // 리액티비티
			resetProgress(); // 프로그레스 리셋
		}
	}

	function resetProgress() {
		clearInterval(timer); // 이전 타이머 초기화
		bonus = 100;
		timer = setInterval(() => {
			bonus -= 1;
			if (bonus === 0) {
				clearInterval(timer);
			}
		}, 40);
	}

	// 멈추기, 그만하기
	function stop() {
		idx = -1;
		clearInterval(timer);
		current = null;
		isRunning = false;
		alert(`수고하셨어요. 총 점수는 ${$score} 입니다 `);
		goto('/save'); // 점수 저장화면으로 이동
	}

	function goHome() {
		goto('/');
	}

	//
</script>

<svelte:head>
	<title>Brain Color - 게임하기</title>
</svelte:head>

<h1 style="text-align: center;">게임 하기</h1>

<!-- 화면 영역 -->
{#if !isRunning}
	<!-- 게임 설명 모드 -->
	<h1 style="text-align:center">게임하는 방법</h1>
	<p>
		<b>1. 문제로 주어진 텍스트에 배경색이 [없을] 경우</b><br />
		-> 문제의 텍스트와 선택지의 글자색이 같은 것을 고릅니다<br /><br />

		<b>2. 문제로 주어진 텍스트에 배경색이 [있을] 경우</b><br />
		=> 문제의 배경색과 선택지의 텍스트가 같은 것을 고릅니다<br /><br />

		tip: 빨리 맞출수록 점수가 많아요!! 늦게 맞춰도 점수는 있으니 진행바에 쫄지 마세요.<br /><br />
		준비되었나요? 이제 시작 버튼을 누르고 도전!!
	</p>
{:else}
	<!-- 문제풀기 모드 구현 -->

	<p>총 점수 : {$score}</p>
	<div style="width: 100%;">
		<progress style="width: 100%;" value={bonus} max="100" />
	</div>
	<p />
	<p>{idx + 1} 번 문제!</p>

	<!-- 문제와 선택지 -->
	{#if idx % 2 === 0}
		<!-- [ 색깔 찾기 ] -->
		<div style="color:{current.question.color}; background-color:white;" class="question">
			{kolor[current.question.text]}
		</div>
		{#each current.choice as item}
			<button
				style="color:{item.color};background-color:white;"
				class="choice"
				on:click={() => {
					$score = $score + (current.question.text === item.color ? 10 + bonus : 0);
					next();
				}}
			>
				{kolor[item.text]}
			</button>
		{/each}
	{:else}
		<!-- [텍스트 찾기] -->
		<div style="background-color:{current.question.color}; color:white;" class="question">
			{kolor[current.question.text]}
		</div>
		<p />
		{#each current.choice as item}
			<button
				style="background-color:{item.color};color:white;"
				class="choice"
				on:click={() => {
					$score = $score + (current.question.color === item.text ? 10 + bonus : 0);
					next();
				}}
			>
				{kolor[item.text]}
			</button>
		{/each}

		<!--  -->
	{/if}
{/if}

<!-- 하단 버튼 영역 -->
<p style="text-align:center;">
	{#if isRunning}
		<button style="width: 45%; height:60px; font-weight:bold; font-size:30px" on:click={stop}>
			그만하기
		</button>
	{:else}
		<button style="width: 45%; height:60px; font-weight:bold; font-size:30px" on:click={start}>
			시작
		</button>
	{/if}
	<button style="width: 45%; height:60px; font-weight:bold; font-size:30px" on:click={goHome}>
		홈으로
	</button>
</p>

<!--  -->
<style>
	.question {
		margin: 0 auto;
		text-align: center;
		text-align: center;
		text-shadow: 1px 1px 1px #000;

		font-weight: bold;
		font-size: 60px;
		width: 150px;
	}
	.choice {
		text-shadow: 1px 1px 1px #000;
		width: 50%;
		height: 150px;
		font-weight: bold;
		font-size: 50px;
	}
</style>
