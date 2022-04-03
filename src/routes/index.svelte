<script>
	import { goto } from '$app/navigation';
	// import { scoreList } from '$lib/store';
	import { onMount } from 'svelte';

	// 데이터 조회
	async function getPosts() {
		const res = await fetch('/api/score', { method: 'GET' });
		const json = await res.json();
		return json.list;
	}

	// 서버 데이터 수신감지 promise 변수
	let promisePosts = [];

	onMount(async () => {
		promisePosts = await getPosts();
		console.log('index Mounted');
	});

	//
</script>

<svelte:head>
	<title>Brain Color</title>
</svelte:head>

<h1 style="text-align: center;">두뇌개발게임</h1>

<h3 style="text-align: center;">이번 주의 점수</h3>
<table style="width: 100%;">
	<thead>
		<tr>
			<th>No</th>
			<th>점수</th>
			<th>이름</th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<td style="text-align: center;"> ========== </td>
			<td style="text-align: center;"> ========== </td>
			<td style="text-align: center;"> ========== </td>
		</tr>

		{#await promisePosts}
			<tr>
				<td colspan="3" style="text-align: center;">
					<h4>최신점수를 가져오는 중 ...</h4>
				</td>
			</tr>
		{:then list}
			{#each list as item, index}
				<tr>
					<td style="text-align: center;">
						{index + 1}
					</td>
					<td style="text-align: center;">
						{item.score}
					</td>
					<td style="text-align: center;">
						{item.name}
					</td>
				</tr>
			{/each}
		{/await}
	</tbody>
</table>

<!-- 게임하기 화면으로 이동 -->
<p style="text-align: center;">
	<button
		style="width: 100%; height: 60px; font-weight:bold; font-size:30px"
		on:click={() => {
			goto('/quiz');
		}}
	>
		게임하기
	</button>
</p>
