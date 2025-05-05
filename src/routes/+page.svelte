<script lang="ts">
	let searchQuery = $state('')
	let selectedCategory = $state('')
	let results: any[] = $state([])
	let error:string = $state('')
	let loading = $state(false)
	let clicked = $state(false)


	type FullData = Record<string, Candidate[]>

	let fullData: FullData = $state({})

	const categories = [
		['programming', 'Programming1'],
		['content', 'Content Creation1'],
		['marketing', 'Marketing1'],
		['design', 'Design & Creativity1']
	]
	interface Candidate {
		firstName: string
		lastName: string
		interviewRefNo: string
		major: string
	}

	async function handleSearch() {
		if (!selectedCategory) {
			error = 'Please select a category.'
			return
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
				throw new Error('Failed to fetch data')			}
		} catch (e) {
			console.log("error = 'Failed to fetch or filter data.'")

			error = 'Failed to fetch or filter data.'
		} finally {
			loading = false
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

<div class="min-w-full min-h-[90svh] bg-black   text-white">
<section  class="bg-black  sticky top-0 ">
	<nav
		class=" mx-auto flex max-w-6xl items-center justify-between bg-black px-8 py-4 transition-all"
	>
		<div class="flex items-center justify-between gap-8">
			<a href="https://ywc20.ywc.in.th"
				><img
					alt="ywc main logo"
					loading="lazy"
					width="64"
					height="32"
					decoding="async"
					data-nimg="1"
					style="color:transparent"
					src="https://ywc20.ywc.in.th/logo-ywc20-mono.png"
				/></a
			>
			<div class="hidden gap-8 text-white lg:flex">
				<a href="https://ywc20.ywc.in.th#about">About</a><a href="https://ywc20.ywc.in.th#major">Majors</a><a href="https://ywc20.ywc.in.th#timeline">Timeline</a><a
					href="https://ywc20.ywc.in.th#location">Location</a
				><a href="https://ywc20.ywc.in.th#faq">FAQ</a><a href="https://ywc20.ywc.in.th#sponsors">Sponsors</a>
			</div>
		</div>
		<div class="cta_nav">
			<a href="https://register.ywc20.ywc.in.th"
				><button
					class="bg-y20c1 to-y20c2 flex gap-2 rounded-lg bg-gradient-to-r from-20% px-5 py-3 font-semibold text-white hover:opacity-90"
					><span class="text-xs sm:text-sm">ดูผลการคัดเลือก<br />เข้ารอบสัมภาษณ์</span></button
				></a
			>
		</div>
	</nav>
</section>

<section class="mx-auto max-w-7xl space-y-4  pt-8 px-8 ">
	<h1 class="text-2xl text-center font-bold bg-y20c2 to-y20c1 bg-gradient-to-r from-0.5% text-transparent bg-clip-text">ค้นหาผู้มีสิทธิ์เข้าร่วมโครงการ <br> รอบสัมภาษณ์</h1>

	<div class="flex flex-col gap-2 sm:flex-row   bg-y20c3">
		<input
			type="text"
			placeholder="Search by name or ref no..."
			bind:value={searchQuery}
			onkeydown={filterOntype}
			class="flex-1 rounded border px-3 py-2 placeholder-gray-400  bg-y20c3"
		/>

		<select bind:value={selectedCategory} class="rounded border px-2 py-2 text-gray-400  bg-y20c3">
			<option value="">-- Select Category --</option>
			{#each categories as cat}
				<option value={cat[0]}>{cat[1]}</option>
			{/each}
		</select>

		<button onclick={handleSearch} class="rounded font-bold bg-y20c1 to-y20c2 bg-gradient-to-r from-20% px-4 py-2 text-white ">
			Search
		</button>

	</div>

	{#if error}
		<p class="text-red-500">{error}</p>
	{/if}

	{#if loading}
		<p>Loading...</p>
	{:else if results.length > 0}
		<div class="mt-4 gap-4 grid grid-cols-2 text-gray-800">
			{#each results as item}
				<div class="rounded border bg-gray-50 p-3">
					<p><strong>{item.firstName} {item.lastName}</strong></p>
					<p>Ref: {item.interviewRefNo}</p>
					<p>Major: {item.major}</p>
				</div>
			{/each}
		</div>
	{:else if results.length == 0 && clicked !== false}
		<p class="text-gray-500">No matching results.</p>
	{/if}
</section>

</div>
<section class="  mx-auto space-y-4  pt-8 px-8 bg-black text-gray-400  ">
	<footer id="footer" class=" max-w-7xl container mx-auto px-8 flex flex-col items-center justify-center  md:grid grid-cols-1 md:grid-cols-7 pb-8"><div class="col-span-1"><a href="https://www.webmaster.or.th/" target="_blank"><img alt="Main logo" loading="lazy" width="63" height="32" decoding="async" data-nimg="1" style="color:transparent" src="https://ywc20.ywc.in.th/twa.svg"></a></div><div class="col-span-1 md:col-span-3 flex gap-8 py-4"><a class="footer-link sm:text-sm" href="https://ywc20.ywc.in.th/privacy-policy">Privacy Policy</a><a class="footer-link sm:text-sm" href="https://ywc20.ywc.in.th/terms">Terms and Conditions</a><a class="footer-link sm:text-sm" href="https://sponsor.ywc.in.th">Sponsorship</a></div><div class="col-span-1 md:col-span-3"><p class="footer-text text-center md:text-right">Copyright 2003 - 2025. Young Webmaster Camp,</p><p class="footer-text text-center md:text-right">in association with Thai Webmaster and Online Media Association, all rights reserved.</p></div></footer>
</section>
