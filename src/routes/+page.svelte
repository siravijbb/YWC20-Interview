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
</script>

<section class="bg-black">
	<nav
		class="sticky top-0 mx-auto flex max-w-6xl items-center justify-between bg-black px-8 py-4 transition-all"
	>
		<div class="flex items-center justify-between gap-8">
			<a href="/"
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
				<a href="#about">About</a><a href="#major">Majors</a><a href="#timeline">Timeline</a><a
					href="#location">Location</a
				><a href="#faq">FAQ</a><a href="#sponsors">Sponsors</a>
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

<section class="mx-auto max-w-xl space-y-4 bg-blue-400 p-6">
	<h1 class="text-2xl font-bold">Search Candidates</h1>

	<div class="flex flex-col gap-2 sm:flex-row">
		<input
			type="text"
			placeholder="Search by name or ref no..."
			bind:value={searchQuery}
			class="flex-1 rounded border px-3 py-2"
		/>

		<select bind:value={selectedCategory} class="rounded border px-2 py-2">
			<option value="">-- Select Category --</option>
			{#each categories as cat}
				<option value={cat[0]}>{cat[1]}</option>
			{/each}
		</select>

		<button onclick={handleSearch} class="rounded bg-blue-600 px-4 py-2 text-white">
			Search
		</button>
	</div>

	{#if error}
		<p class="text-red-500">{error}</p>
	{/if}

	{#if loading}
		<p>Loading...</p>
	{:else if results.length > 0}
		<div class="mt-4 space-y-2">
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
