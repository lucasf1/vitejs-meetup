<script>
    import meetups from "./meetup-store";
    import { createEventDispatcher } from "svelte";
    import { isEmpty, isValidEmail } from "../helpers/validations.js";
    import TextInput from "../UI/TextInput.svelte";
    import Button from "../UI/Button.svelte";
    import Modal from "../UI/Modal.svelte";

    export let id = null;

    let title = "";
    let subtitle = "";
    let address = "";
    let email = "";
    let description = "";
    let imageUrl = "";

    if (id) {
        const unsubscribe = meetups.subscribe((items) => {
            const selectedMeetup = items.find((i) => i.id === id);
            title = selectedMeetup.title;
            subtitle = selectedMeetup.subtitle;
            address = selectedMeetup.address;
            email = selectedMeetup.email;
            description = selectedMeetup.description;
            imageUrl = selectedMeetup.imageUrl;
        });

        unsubscribe();
    }

    const dispatch = createEventDispatcher();

    $: titleValid = !isEmpty(title);
    $: subtitleValid = !isEmpty(subtitle);
    $: addressValid = !isEmpty(address);
    $: descriptionValid = !isEmpty(description);
    $: imageUrlValid = !isEmpty(imageUrl);
    $: emailValid = isValidEmail(email);
    $: formIsValid =
        titleValid &&
        subtitleValid &&
        addressValid &&
        descriptionValid &&
        imageUrlValid &&
        emailValid;

    function submitForm() {
        const meetupData = {
            title: title,
            subtitle: subtitle,
            description: description,
            imageUrl: imageUrl,
            contactEmail: email,
            address: address,
        };

        if (id) {
            meetups.updateMeetup(id, meetupData);
        } else {
            meetups.addMeetup(meetupData);
        }

        dispatch("save");
    }

    function cancel() {
        dispatch("cancel");
    }

    function deleteMeetup(){
        meetups.removeMeetup(id);
        dispatch("save");
    }
</script>

<Modal title="Edit Meetup Data" on:cancel>
    <form on:submit|preventDefault={submitForm}>
        <TextInput
            id="title"
            label="Title"
            valid={titleValid}
            validityMessage="Please enter a valid title"
            value={title}
            on:input={(event) => (title = event.target.value)}
        />
        <TextInput
            id="subtitle"
            label="Subtitle"
            valid={subtitleValid}
            validityMessage="Please enter a valid subtitle"
            value={subtitle}
            on:input={(event) => (subtitle = event.target.value)}
        />
        <TextInput
            id="address"
            label="Address"
            valid={addressValid}
            validityMessage="Please enter a valid address"
            value={address}
            on:input={(event) => (address = event.target.value)}
        />
        <TextInput
            id="imageUrl"
            label="Image URL"
            valid={imageUrlValid}
            validityMessage="Please enter a valid image Url"
            value={imageUrl}
            on:input={(event) => (imageUrl = event.target.value)}
        />
        <TextInput
            id="email"
            label="E-mail"
            valid={emailValid}
            validityMessage="Please enter a valid email address"
            value={email}
            type="email"
            on:input={(event) => (email = event.target.value)}
        />
        <TextInput
            id="description"
            label="Description"
            controlType="textarea"
            valid={descriptionValid}
            validityMessage="Please enter a valid description"
            bind:value={description}
        />
    </form>
    <div slot="footer">
        <Button type="button" mode="outline" on:click={cancel}>Cancel</Button>
        <Button type="button" on:click={submitForm} disabled={!formIsValid}
            >Save</Button
        >
        {#if id}
            <Button type="button" on:click={deleteMeetup}>Delete</Button>
        {/if}
    </div>
</Modal>

<style>
    form {
        width: 100%;
    }
</style>
