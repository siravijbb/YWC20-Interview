<script lang="ts">
	let searchQuery = $state('');;
	let selectedCategory = $state('');
	let results: any[] = $state([]);
	let error = $state('');
	let loading = $state(false);

	type FullData = Record<string, Candidate[]>;

	let fullData: FullData = $state({});


	const categories = ([
		['programming', 'Programming1'],
		['content', 'Content Creation1'],
		['marketing', 'Marketing1'],
		['design', 'Design & Creativity1']
	]);
	interface Candidate {
		firstName: string;
		lastName: string;
		interviewRefNo: string;
		major: string;
	}



	async function handleSearch() {
		if (!selectedCategory) {
			error = 'Please select a category.';
			return;
		}

		error = '';
		loading = true;
		results = [];

		try {
			const response = await fetch('https://api.ywc20.ywc.in.th/homework/candidates', {
				headers: {
					'Content-Type': 'application/json',
					'x-reference-id': 'PG02'
				},
			});
			if (!response.ok) throw new Error('Failed to fetch data');
			fullData = await response.json();

			const categoryData = fullData[selectedCategory] || [];

			// Simple search: match query in firstName, lastName, or interviewRefNo
			const q = searchQuery.toLowerCase();
			results = categoryData.filter(
				(item) =>
					item.firstName.toLowerCase().includes(q) ||
					item.lastName.toLowerCase().includes(q) ||
					item.interviewRefNo.toLowerCase().includes(q)
			);
		} catch (e) {
			error = 'Failed to fetch or filter data.';
		} finally {
			loading = false;
		}
	}
</script>

<div class="p-6 max-w-xl mx-auto space-y-4">
	<h1 class="text-2xl font-bold">Search Candidates</h1>

	<div class="flex flex-col sm:flex-row gap-2">
		<input
			type="text"
			placeholder="Search by name or ref no..."
			bind:value={searchQuery}
			class="flex-1 border rounded px-3 py-2"
		/>

		<select bind:value={selectedCategory} class="border rounded px-2 py-2">
			<option value="">-- Select Category --</option>
			{#each categories as cat}
				<option value={cat[0]}>{cat[1]}</option>
			{/each}
		</select>

		<button on:click={handleSearch} class="bg-blue-600 text-white px-4 py-2 rounded">
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
				<div class="p-3 border rounded bg-gray-50">
					<p><strong>{item.firstName} {item.lastName}</strong></p>
					<p>Ref: {item.interviewRefNo}</p>
					<p>Major: {item.major}</p>
				</div>
			{/each}
		</div>
	{:else if selectedCategory}
		<p class="text-gray-500">No matching results.</p>
	{/if}
</div>
