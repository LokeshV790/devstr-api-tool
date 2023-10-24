<script>
	import { tools } from "../../tools";
	import { onMount } from "svelte";
	import axios from "axios";
	import Intro from "$lib/Intro.svelte";
	import { Search, Button, Dropdown, DropdownItem } from 'flowbite-svelte';
	import { ChevronDownSolid } from 'flowbite-svelte-icons';
  
	const items = [{ label: 'GET' }, { label: 'POST' }, { label: 'PUT' }, { label: 'DELETE' }];
  
	let selectCategory = 'GET';
	let url = "";
	let requestData = "";
	let responseData = {
	  status: null,
	  headers: null,
	  data: null,
	};
	let errorResponse = null;
  
	// Detect whether the code is running on the client
	let client = false;
	onMount(() => {
	  client = true;
	});
  
	const handleRequest = async () => {
	  try {
		let response;
  
		if (selectCategory === "GET") {
		  response = await axios.get(url);
		} else if (selectCategory === "POST") {
		  response = await axios.post(url, { data: requestData });
		} else if (selectCategory === "PUT") {
		  response = await axios.put(url, { data: requestData });
		} else if (selectCategory === "DELETE") {
		  response = await axios.delete(url);
		}
  
		// Store the entire response object
		responseData.status = response.status;
		responseData.headers = response.headers;
		responseData.data = response.data;
  
		errorResponse = null; // Clear any previous error message
	  } catch (error) {
		errorResponse = `Error: ${error.message}`;
		responseData = {
		  status: null,
		  headers: null,
		  data: null,
		}; // Clear any previous response data
	  }
	};
  
	let apiTesterTitle = tools["api-tester"].name;
	let apiTesterDesc = tools["api-tester"].description;
  </script>
  
  <section class="api-tester">
	<Intro heading={apiTesterTitle} description={apiTesterDesc} />
  
	<form class="flex w-3/4 mx-auto">
	  <div class="relative">
		<Button class="rounded-e-none whitespace-nowrap border border-r-0 border-primary-700">
		  {selectCategory}
		  <ChevronDownSolid class="w-2.5 h-2.5 ml-2.5" />
		</Button>
		<Dropdown classContainer="w-40">
		  {#each items as { label }}
		  <DropdownItem
			on:click={() => {
			  selectCategory = label;
			}}
			class={selectCategory === label ? 'underline' : ''}
		  >
			{label}
		  </DropdownItem>
		  {/each}
		</Dropdown>
	  </div>
	  <input
	  type="text"
	  bind:value={url}
	  placeholder="Enter API URL..."
	  class="rounded-none py-2.5 input"
	/>
	  <!-- <Search size="md" class="rounded-none py-2.5" placeholder="Enter Your API..." /> -->
	  {#if client}
		<Button class="!p-2.5 rounded-s-none" on:click={handleRequest}>
		  SEND
		</Button>
	  {/if}
	</form>
  </section>
  
  <div class="container">
	<button id="getRequestBtn">GET request</button>
	<button id="postRequestBtn">POST request</button>
	<button id="putRequestBtn">PUT request</button>
	<button id="deleteRequestBtn">DELETE request</button>
  
	<input
	  type="text"
	  bind:value={requestData}
	  placeholder="Enter data (for POST and PUT)"
	  class="input"
	/>
  </div>
  
  {#if responseData.status !== null}
  <div class="response-data">
	<h2>Response Status</h2>
	<pre>Status Code: {responseData.status}</pre>
	<h2>Response Data</h2>
	<pre>{JSON.stringify(responseData.data, null, 2)}</pre>
  </div>
  {/if}
  
  <style>
	.container {
	  margin: 20px;
	}
  
	button {
	  background-color: #4caf50;
	  color: white;
	  border: none;
	  padding: 10px 20px;
	  font-size: 16px;
	  cursor: pointer;
	  margin-right: 10px;
	}
  
	.input {
	  width: 100%;
	  padding: 10px;
	  margin: 8px 0;
	  box-sizing: border-box;
	  font-size: 16px;
	}
  
	.response-data {
	  border: 1px solid #ccc;
	  padding: 10px;
	  margin-top: 20px;
	  background-color: #f9f9f9;
	}
  
	h2 {
	  color: #333;
	}
  
	pre {
	  white-space: pre-wrap;
	}
  
	.error {
	  color: red;
	}
  </style>
  