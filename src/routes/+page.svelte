<script lang="ts">
	import Navbar from '$components/navbar.svelte';
	import Footer from '$components/footer.svelte';
	import { onMount } from 'svelte';
	let mappedInterview = $state('');

	let searchQuery = $state('');
	let selectedCategory = $state('');
	let results: any[] = $state([]);
	let error: string = $state('');
	let loading = $state(false);
	let clicked = $state(false);

	type FullData = Record<string, Candidate[]>;
	interface Candidate {
		firstName: string;
		lastName: string;
		interviewRefNo: string;
		major: string;
	}
	let fullData: FullData = $state({});
	let SearchLimit = 0




	const categories = [
		['design', 'สาขา Web Design'],
		['programming', 'สาขา Web Programming'],
		['marketing', 'สาขา Web Marketing'],
		['content', 'สาขา Web Content']
	];


	async function handleSearch() {
		if (!selectedCategory) {
			return (error = 'กรุณาเลือกสาขาที่ต้องการค้นหา');
		}
		if(SearchLimit >= 5) {
			alert('กรุณารอ 10 วินาทีก่อนค้นหาอีกรอบ');
		}
		SearchLimit++

		error = '';
		loading = true;
		results = [];
		clicked = true;

		try {
			const response = await fetch('https://api.ywc20.ywc.in.th/homework/candidates', {
				headers: {
					'Content-Type': 'application/json',
					'x-reference-id': 'PG02'
				}
			});
			if (response.status === 200) {
				RedirectToInterviewInfo()


				fullData = await response.json();

				filterOntype()


			} else {
				throw new Error(`fetch fail : ${response.status} ${response.statusText}`);
			}
		} catch (e) {
			console.log("error = 'Failed to fetch or filter data.'");

			error = 'ไม่สามารถค้นหาได้ขณะนี้ กรุณาลองใหม่ภายหลัง';
		} finally {
			 loading = false;
		}
	}
	function filterOntype() {
		const categoryData = fullData[selectedCategory] || [];
		RedirectToInterviewInfo();

		// Simple search: match query in firstName, lastName, or interviewRefNo
		const q = searchQuery.toLowerCase();results = categoryData
			.filter(
				(item) =>
					item.firstName.toLowerCase().includes(q) ||
					item.lastName.toLowerCase().includes(q) ||
					item.interviewRefNo.toLowerCase().includes(q)
			)
			.sort((a, b) => {
				// Sort by firstName first, then lastName
				const firstNameComparison = a.firstName.localeCompare(b.firstName);
				if (firstNameComparison === 0) {
					return a.lastName.localeCompare(b.lastName);
				}
				return firstNameComparison;
			});
	}
	function RedirectToInterviewInfo(){
		if(selectedCategory === "design") {
			mappedInterview = "https://ywc20.ywc.in.th/interview/design"
		}
		else if(selectedCategory === "programming") {
			mappedInterview = "https://ywc20.ywc.in.th/interview/programming"
		}
		else if(selectedCategory === "marketing") {
			mappedInterview = "https://ywc20.ywc.in.th/interview/marketing"
		}
		else if(selectedCategory === "content") {
			mappedInterview = "https://ywc20.ywc.in.th/interview/content"
		}else {
			mappedInterview = "#"
		}
	}


	function ClearLimit() { // clear search limit
		SearchLimit = 0;
	}

	onMount(() => {
		let interval = setInterval(ClearLimit, 10000); // Poll every 5 seconds
		return () => {
			clearInterval(interval);
		};
	});
</script>

<head>
	<title>ระบบค้นหาผู้เข้าสัมภาษณ์ Young Webmaster Camp 20</title>
</head>

<div
	class=" bg-y20c3 min-h-[80vh] text-white transition-all sm:min-h-[76.3svh] lg:min-h-[91.5svh] 2xl:min-h-screen"
>
		<Navbar />


	<section class="mx-auto max-w-7xl space-y-4 p-8">
		<h1
			class="bg-y20c2 to-y20c1 from-0.5% bg-gradient-to-r bg-clip-text text-center text-2xl font-bold text-transparent"
		>
			ค้นหาผู้มีสิทธิ์เข้าร่วมโครงการ <br /> รอบสัมภาษณ์
		</h1>

		<div class="bg-y20c3 flex flex-col gap-2 sm:flex-row">
			<input
				type="text"
				placeholder="ค้นหาโดยชื่อหรือเลขประจำตัว"
				bind:value={searchQuery}
				onkeyup={(e) => e.key === 'Enter' ? handleSearch() : filterOntype()}
				onkeydown={filterOntype}
				class="bg-y20c3 flex-1 rounded border px-3 py-2 placeholder-gray-400"
			/>

			<select
				bind:value={selectedCategory}
				onchange={filterOntype}
				class="bg-y20c3 rounded border px-2 py-2 text-gray-400"
			>
				<option value="">เลือกสาขา</option>
				{#each categories as cat}
					<option value={cat[0]}>{cat[1]}</option>
				{/each}
			</select>

			<button
				onclick={handleSearch}
				class="bg-y20c1 to-y20c2 hover:bg-y20c1/60 hover:to-y20c2/60 rounded bg-gradient-to-r from-20% px-4 py-2 font-bold text-white transition-all"
			>
				ค้นหา
			</button>
		</div>

		{#if error}
			<p class="text-center text-gray-400 md:text-left">{error}</p>
		{/if}

		{#if loading}
			<p class="text-center text-gray-400 md:text-left">กำลังค้นหา กรุณารอ...</p>
		{:else if results.length > 0}
			<h2
				class="bg-y20c2 to-y20c1 from-0.5% bg-gradient-to-r bg-clip-text text-center text-lg font-bold text-transparent"
			>
				รายชื่อผู้มีสิทธิ์เข้าร่วมโครงการ
			</h2>
			{#if selectedCategory === 'design'}
				<h2
					class="bg-y20c2 to-y20c1 from-0.5% -mt-4 bg-gradient-to-r bg-clip-text text-center text-lg font-bold text-transparent"
				>
					สาขา: <b>Web Design</b>
				</h2>
			{:else if selectedCategory === 'programming'}
				<h2
					class="bg-y20c2 to-y20c1 from-0.5% -mt-4 bg-gradient-to-r bg-clip-text text-center text-lg font-bold text-transparent"
				>
					สาขา: <b>Web Programming</b>
				</h2>
			{:else if selectedCategory === 'marketing'}
				<h2
					class="bg-y20c2 to-y20c1 from-0.5% -mt-4 bg-gradient-to-r bg-clip-text text-center text-lg font-bold text-transparent"
				>
					สาขา: <b>Web Marketing</b>
				</h2>
			{:else if selectedCategory === 'content'}
				<h2
					class="bg-y20c2 to-y20c1 from-0.5% -mt-4 bg-gradient-to-r bg-clip-text text-center text-lg font-bold text-transparent"
				>
					สาขา: <b>Web Content</b>
				</h2>
			{/if}

			<div class="mt-4 grid grid-cols-1 gap-4 text-white">
				{#each results as item}
					<div
						class="bg-y20c1/80 to-y20c2/80 hover:bg-y20c1/40 hover:to-y20c2/40 rounded bg-gradient-to-r p-3 transition-all"
					>
						<a href={mappedInterview} >
							<p class="text-center text-lg"><strong>{item.firstName} {item.lastName}</strong></p>
							<p class="text-center text-lg">
								เลขประจำตัวผู้เข้าสัมภาษณ์: <b>{item.interviewRefNo}</b>
							</p>
							{#if item.major === 'web_design'}
								<p class="text-center text-lg">สาขา: <b>Web Design</b></p>
							{:else if item.major === 'web_programming'}
								<p class="text-center text-lg">สาขา: <b>Web Programming</b></p>
							{:else if item.major === 'web_marketing'}
								<p class="text-center text-lg">สาขา: <b>Web Marketing</b></p>
							{:else if item.major === 'web_content'}
								<p class="text-center text-lg">สาขา: <b>Web Content</b></p>
							{/if}
						</a>
					</div>
				{/each}
			</div>
		{:else if results.length === 0 && clicked !== false && error === '' && selectedCategory !== '' }
			<p class="text-center text-gray-400 md:text-left">ไม่พบผลการค้นหา กรุณาลองใหม่อีกครั้ง</p>
		{/if}
	</section>
</div>
	<Footer />
