<script setup>
  import { ref,onMounted, watch } from 'vue'
  
  const contacts = ref([])
  const firstName = ref('')
  const lastName = ref('')
  const phoneNumber = ref('')
  const photo = ref('')
  const selectedContact = ref(null)
  const showCreateContactSection = ref(false)
  
  const addContact = () => {
	if (!firstName.value.trim() || !lastName.value.trim() || !phoneNumber.value.trim()) {
	  return
	}
  
	const newContact = {
	  id: Date.now(), 
	  firstName: firstName.value,
	  lastName: lastName.value,
	  phoneNumber: phoneNumber.value,
	  photo: photo.value
	}
  
	
	let index = contacts.value.findIndex(contact => contact.firstName.localeCompare(newContact.firstName) > 0);

	
	if (index === -1) {
	index = contacts.value.length;
	}

	
	contacts.value.splice(index, 0, newContact);

	/* Clear input area after adding contact */
	firstName.value = '';
	lastName.value = '';
	phoneNumber.value = '';
	photo.value = '';
  }
  
  const handlePhotoChange = (event) => {
	const file = event.target.files[0]
	if (file) {
	  const reader = new FileReader()
	  reader.onload = () => {
		photo.value = reader.result
	  }
	  reader.readAsDataURL(file)
	}
  }
  
  const showContact = (contact) => {
	selectedContact.value = contact
  }
  
  const toggleCreateContactSection = () => {
	showCreateContactSection.value = !showCreateContactSection.value
  }

	watch(contacts, (newVal) => {
	localStorage.setItem('contacts', JSON.stringify(newVal))
	}, { deep: true })
	
	onMounted(() => {
	const savedContacts = localStorage.getItem('contacts')
	if (savedContacts) {
		contacts.value = JSON.parse(savedContacts)
	}
	})
	localStorage.setItem('contacts', JSON.stringify(contacts.value))

  </script>
<template>
	<main class="app">
	  <button @click="toggleCreateContactSection">Create Contact</button>
  
	  <section class="create-contact" v-if="showCreateContactSection">
		<h3>CREATE A CONTACT</h3>
		<form id="new-contact-form" @submit.prevent="addContact"> <!-- handle the form submission -->
		  <label for="first-name">First Name:</label>
		  <input type="text" id="first-name" v-model="firstName" />
		  
		  <label for="last-name">Last Name:</label>
		  <input type="text" id="last-name" v-model="lastName" />
		  
		  <label for="phone">Phone Number:</label>
		  <input type="tel" id="phone" v-model="phoneNumber" />
		  
		  <label for="photo">Photo:</label>
		  <input type="file" id="photo" accept="image/*" @change="handlePhotoChange" />
		  
		  <input type="submit" value="Done" />
		</form>
	  </section>
	  
	  <section class="contact-list">
		<h3>CONTACT LIST</h3>
		<div class="list">
		  <div v-for="contact in contacts" :key="contact.id" @click="showContact(contact)">
			{{ contact.firstName }} {{ contact.lastName }}
		  </div>
		  <!--  used for rendering a list of items based on an array. -->
		</div>
	  </section>
	  
	  <section class="contact-details" v-if="selectedContact">
		<h3>CONTACT DETAILS</h3>
		<div>
		  <div>First Name: {{ selectedContact.firstName }}</div>
		  <div>Last Name: {{ selectedContact.lastName }}</div>
		  <div>Phone Number: {{ selectedContact.phoneNumber }}</div>
		  <div>
			<img :src="selectedContact.photo" alt="Contact Photo" v-if="selectedContact.photo" style="max-width: 200px; max-height: 200px;" />
		  </div>
		</div>
	  </section>
	</main>
  </template>
  
  
  
 
  