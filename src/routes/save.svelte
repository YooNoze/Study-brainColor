<script>
	import { goto } from '$app/navigation';
	import { score, scoreList } from '$lib/store';

	let name = '--';

	// REST API PUT 메서드로 보낼 변수
	let bodyData;

	// 비활성화 상태 모드 변수
	let disabled = false;
	let message = '이름을 입력하고 저장 버튼을 누르세요';

	// HTML 블록에 값 name 이 변경되면 bodyData 를 자동으로 업데이트
	$: bodyData = { score: $score, name };

	async function saveScore() {
		disabled = true;

		message = '점수 저장중 ...';

		// API PUT Method 호출
		let rtn = await fetch('/api/score', {
			method: 'PUT',
			body: JSON.stringify(bodyData)
		});

		// await 종류 후 팝업
		if (rtn.status === 200) {
			alert('저장 됨');
		}

		/* 		// 내 점수를 scoreList 에 넣습니다
		$scoreList.push({ score: $score, name });

		// 정렬
		$scoreList.sort((a, b) => b.score - a.score);

		// 10개를 맞추기 위해서 한개를 꺼내서 버립니다
		$scoreList.pop(); */

		// 홈으로 이동
		goto('/');
	}

	//
</script>

<svelte:head>
	<title>Brain Color - 점수 저장하기</title>
</svelte:head>

<h1 style="text-align: center;">점수 저장하기</h1>

<h4>{message}</h4>

<p>획득점수 : {$score}</p>

<p>이름 : <input type="text" bind:value={name} {disabled} /></p>

<button on:click={saveScore} {disabled}> 저장 </button>
