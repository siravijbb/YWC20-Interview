<script lang="ts">
	import Navbar from "$components/navbar.svelte";


	let searchQuery = $state('')
	let selectedCategory = $state('')
	let results: any[] = $state([])
	let error:string = $state('')
	let loading = $state(false)
	let clicked = $state(false)


	type FullData = Record<string, Candidate[]>

	let fullData: FullData = $state({})

	const categories = [
		['design', 'สาขา Web Design'],
		['programming', 'สาขา Web Programming'],
		['marketing', 'สาขา Web Marketing'],
		['content', 'สาขา Web Content'],
	]
	interface Candidate {
		firstName: string
		lastName: string
		interviewRefNo: string
		major: string
	}

	async function handleSearch() {
		if (!selectedCategory) {

			return error = 'กรุณาเลือกสาขาที่ต้องการค้นหา'
		}

		error = ''
		loading = true
		results = []
		clicked = true

		try {
			const response = await fetch('https://api.ywc20.ywc.in.th/homework/candidates', {
				headers: {
					'Content-Type': 'application/json',
					'x-reference-id': 'PG02'
				}
			})
			if (response.status === 200) {
				fullData = await response.json()


				const categoryData = fullData[selectedCategory] || []

				// Simple search: match query in firstName, lastName, or interviewRefNo
				const q = searchQuery.toLowerCase()
				results = categoryData.filter(
					(item) =>
						item.firstName.toLowerCase().includes(q) ||
						item.lastName.toLowerCase().includes(q) ||
						item.interviewRefNo.toLowerCase().includes(q)
				)
			} else {
				throw new Error(`fetch fail : ${response.status} ${response.statusText}`)			}
		} catch (e) {
			console.log("error = 'Failed to fetch or filter data.'")

			error = 'ไม่สามารถค้นหาได้ขณะนี้ กรุณาลองใหม่ภายหลัง'
		} finally {
			return  loading = false
		}
	}
	function filterOntype(){
		const categoryData = fullData[selectedCategory] || []

		// Simple search: match query in firstName, lastName, or interviewRefNo
		const q = searchQuery.toLowerCase()
		results = categoryData.filter(
			(item) =>
				item.firstName.toLowerCase().includes(q) ||
				item.lastName.toLowerCase().includes(q) ||
				item.interviewRefNo.toLowerCase().includes(q)
		)
	}
</script>

<head>
	<title>ระบบค้นหาผู้เข้าสัมภาษณ์ Young Webmaster Camp 20</title>


</head>

<div class=" min-h-[80vh] sm:min-h-[76.3svh] lg:min-h-[91.5svh] 2xl:min-h-screen  bg-y20c3   text-white transition-all">
<section  class="bg-black  md:sticky top-0 ">
	<Navbar />
</section>

<section class="mx-auto max-w-7xl space-y-4  p-8 ">
	<h1 class="text-2xl text-center font-bold bg-y20c2 to-y20c1 bg-gradient-to-r from-0.5% text-transparent bg-clip-text ">ค้นหาผู้มีสิทธิ์เข้าร่วมโครงการ <br> รอบสัมภาษณ์</h1>

	<div class="flex flex-col gap-2 sm:flex-row   bg-y20c3">
		<input
			type="text"
			placeholder="ค้นหาโดยชื่อหรือเลขประจำตัว"
			bind:value={searchQuery}
			onkeydown={filterOntype}
			class="flex-1 rounded border px-3 py-2 placeholder-gray-400  bg-y20c3"
		/>

		<select bind:value={selectedCategory}   onchange={filterOntype} class="rounded border px-2 py-2 text-gray-400  bg-y20c3">
			<option  value="">เลือกสาขา</option>
			{#each categories as cat}
				<option value={cat[0]} >{cat[1]}</option>
			{/each}
		</select>

		<button onclick={handleSearch} class="rounded font-bold bg-y20c1 to-y20c2 hover:bg-y20c1/60 hover:to-y20c2/60 bg-gradient-to-r from-20% px-4 py-2 text-white transition-all">
			ค้นหา
		</button>

	</div>

	{#if error}
		<p class="text-gray-400 text-center md:text-left ">{error}</p>
	{/if}

	{#if loading}
		<p class="text-gray-400 text-center md:text-left ">กำลังค้นหา กรุณารอ...</p>
	{:else if results.length > 0}
		<h2 class="text-center font-bold text-lg bg-y20c2 to-y20c1 bg-gradient-to-r from-0.5% text-transparent bg-clip-text">รายชื่อผู้มีสิทธิ์เข้าร่วมโครงการ</h2>
		{#if selectedCategory === "design"}
			<h2 class="-mt-4 text-center font-bold text-lg bg-y20c2 to-y20c1 bg-gradient-to-r from-0.5% text-transparent bg-clip-text">สาขา: <b>Web Design</b></h2>
		{:else if selectedCategory === "programming"}
			<h2 class="-mt-4 text-center font-bold text-lg bg-y20c2 to-y20c1 bg-gradient-to-r from-0.5% text-transparent bg-clip-text">สาขา: <b>Web Programming</b></h2>
		{:else if selectedCategory === "marketing"}
			<h2 class="-mt-4 text-center font-bold text-lg bg-y20c2 to-y20c1 bg-gradient-to-r from-0.5% text-transparent bg-clip-text">สาขา: <b>Web Marketing</b></h2>
		{:else if selectedCategory === "content"}
			<h2 class="-mt-4 text-center font-bold text-lg bg-y20c2 to-y20c1 bg-gradient-to-r from-0.5% text-transparent bg-clip-text">สาขา: <b>Web Content</b></h2>
		{/if}




		<div class="mt-4 gap-4 grid  grid-cols-1 text-white">
			{#each results as item}
				<div class="rounded  p-3   bg-y20c1/60 to-y20c2/60 hover:bg-y20c1/40 hover:to-y20c2/40 transition-all   bg-gradient-to-r">
					<a href="#">
					<p class="text-center text-lg"><strong>{item.firstName} {item.lastName}</strong></p>
					<p class="text-center text-lg">เลขประจำตัวผู้เข้าสัมภาษณ์: <b>{item.interviewRefNo}</b></p>
						{#if item.major === "web_design"}
							<p class="text-center text-lg">สาขา:<b>Web Design</b></p>
						{:else if item.major === "web_programming"}
							<p class="text-center text-lg">สาขา:<b>Web Programming</b></p>
						{:else if item.major === "web_marketing"}
							<p class="text-center text-lg">สาขา:<b>Web Marketing</b></p>
						{:else if item.major === "web_content"}
							<p class="text-center text-lg">สาขา:<b>Web Content</b></p>
						{/if}
					</a>
				</div>
			{/each}
		</div>
	{:else if results.length == 0 && clicked !== false && error === '' }
		<p class="text-gray-400 text-center md:text-left ">ไม่พบผลการค้นหา กรุณาลองใหม่อีกครั้ง</p>
	{/if}
</section>

</div>
<section class="  mx-auto space-y-4  pt-8 px-8 bg-linear-to-t from-black to-y20c3 from-1% text-gray-400  ">
	<footer id="footer" class=" max-w-7xl container mx-auto px-8 flex flex-col items-center justify-center  md:grid grid-cols-1 md:grid-cols-7 pb-8"><div class="col-span-1"><a href="https://www.webmaster.or.th/" target="_blank"><img alt="Main logo" loading="lazy" width="63" height="32" decoding="async" data-nimg="1" style="color:transparent" src="https://ywc20.ywc.in.th/twa.svg"></a></div><div class="col-span-1 md:col-span-3 flex gap-8 py-4"><a class="footer-link sm:text-sm" href="https://ywc20.ywc.in.th/privacy-policy">Privacy Policy</a><a class="footer-link sm:text-sm" href="https://ywc20.ywc.in.th/terms">Terms and Conditions</a><a class="footer-link sm:text-sm" href="https://sponsor.ywc.in.th">Sponsorship</a></div><div class="col-span-1 md:col-span-3"><p class="footer-text text-center md:text-right">Copyright 2003 - 2025. Young Webmaster Camp,</p><p class="footer-text text-center md:text-right">in association with Thai Webmaster and Online Media Association, all rights reserved.</p></div></footer>
</section>
