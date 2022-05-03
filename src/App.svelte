<script>
	import meetups from "./Meetups/meetup-store";
	import Header from "./UI/Header.svelte";
	import MeetupGrid from "./Meetups/MeetupGrid.svelte";
	import Button from "./UI/Button.svelte";
	import EditMeetup from "./Meetups/EditMeetup.svelte";

	let loadedMeetups = meetups;

	let editMode;

	function addMeetUp(event) {

		editMode = null;
	}

	function cancelEdit() {
		editMode = null;
	}
</script>

<Header />

<main>
	<div class="meetup-controls">
		<Button on:click={() => (editMode = "add")}>New Meetup</Button>
	</div>
	{#if editMode === "add"}
		<EditMeetup on:save={addMeetUp} on:cancel={cancelEdit} />
	{/if}
	<MeetupGrid meetups={$loadedMeetups}/>
</main>

<style>
	main {
		margin-top: 5rem;
	}

	.meetup-controls {
		margin: 1rem;
	}
</style>
