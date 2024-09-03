<script>
	import SvelteTable from "./SvelteTable.svelte";
	//import data from "./data.js";

	// define column configs
	const columns = [
		{
			key: "school",
			title: "School",
			value: (v) => v.school,
			sortable: true,
			//filterOptions: generateFilter("school"),
			searchValue: (v, s) =>
				v.school.toString().toLowerCase().includes(s.toLowerCase()),
			filterPlaceholder: "Search for your school...",
			headerClass: "text-left",
			headerFilterClass: "search-filter"
		},
		{
			key: "district",
			title: "District",
			value: (v) => v.district,
			sortable: true,
			//filterOptions: generateFilter("district"),
			headerClass: "text-left"
		},
		{
			key: "county",
			title: "County",
			value: (v) => v.county,
			sortable: true,
			//filterOptions: generateFilter("county"),
			headerClass: "text-left"
		},
		{
			key: "state",
			title: "State",
			value: (v) =>
				v.state.charAt(0).toUpperCase() +
				v.state.substring(1).toLowerCase(),
			sortable: true,
			//filterOptions: generateFilter("state")
			headerClass: "text-left",
		},
		{
			key: "ncessch",
			title: "NCES ID",
			value: (v) => v.ncessch,
			class: "nces_id",
			sortable: true,
			//renderComponent: {
			//	component: CopyToClipboardComponent,
			//	props: { copyToClipboard },
		//},
	}
	]

	let selectedState = "";
	// let data = [];
	let filteredData = [];
	let states = [];

	// Load only the state information initially
	async function loadStateList() {
		const module = await import('./data.js');
		const data = module.default;
		// Extract unique states from data
		states = Array.from(new Set(data.map(row => row.state)));
	}

	// load data
	async function loadData(state) {
		// Dynamically import the data only when a state is selected
		const module = await import('./data.js');
		data = module.default;
		// Filter the data based on the selected state
		filteredData = data.filter(row => row.state === state);
	}

	// Function to convert to title case
	function toTitleCase(str) {
    return str
      .toLowerCase()
      .split(' ')
      .map(word => word.charAt(0).toUpperCase() + word.slice(1))
      .join(' ');
  	}

	// filter table
	// $: if (selectedState) {
	// 	filteredData = data.filter((row) => row.state === selectedState);
	// }

	// Load state list when component initializes
	loadStateList();
</script>

<div class="container">
	<h1>NCES IDs for Schools</h1>
	<p>This table will help you find the NCES ID for your school. To begin, select your state.</p>
	<div class="selector">
	<select id="state" bind:value={selectedState} on:change={() => loadData(selectedState)}>
		<option value="">-- Select a State --</option>
		{#each states as state}
			<option value={state}>{toTitleCase(state)}</option>
		{/each}
	</select>
	</div>

	<!-- Conditionally Display the Table -->
	 <div class="table">
		{#if selectedState && filteredData.length > 0}
			<p>Next, search for your school. Be sure to confirm the district and/or county before copying the NCES ID.</p>
			<SvelteTable 
				{columns} 
				rows={filteredData} 
				classNameTable={["table table-striped"]}
				classNameThead={["table-primary"]}
				classNameInput={["custom-input"]}
			/>
		{:else if selectedState}
			<p>No data available for the selected state.</p>
		{/if}
	</div>
</div>

<style>
	.container {
		margin-top: 20px;
	}

	:global(.text-left) {
		text-align: left;
	}

	:global(.selector) {
		margin-bottom: 10px;
	}

	:global(.search-filter input) {
		width: 100%;
		box-sizing: border-box;
	}

	:global(#state) {
		padding: 5px;
	}
	
</style>