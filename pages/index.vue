<template>
	<div class="font-sans bg-grey-100 min-h-screen py-20 lg:py-40">
		<div
			:class="[
				'bg-white px-10 py-4 border-t-2 border-blue text-grey-300 fixed top-0 inset-x-0 z-50 flex justify-between transition-opacity duration-300',
				notificationActive
					? 'opacity-100 pointer-events-auto'
					: 'opacity-0 pointer-events-none'
			]"
		>
			<span></span>
			<div class="flex items-center">
				<check class="text-blue mr-5 h-6" />
				<p class="text-xs uppercase">The location has been updated.</p>
			</div>
			<button @click="notificationActive = false">
				<times />
			</button>
		</div>
		<div class="[ content-wrapper ] mx-auto">
			<h2 class="text-6xl text-blue font-light text-center">Offices</h2>
			<div class="mt-16">
				<div
					:class="[
						'[ card-base ] py-6 mb-6 transition-all duration-300 bg-blue text-white cursor-pointer',
						addForm && 'opacity-0'
					]"
					@click="addForm = true"
				>
					<div
						class="flex items-center justify-between"
						@click="addForm = true"
					>
						<p>Add New Location</p>
						<plus />
					</div>
				</div>
				<form-component
					:office="form"
					:editMode="false"
					:form-active="addForm"
					@close-form="addForm = false"
					@add-office="addOffice"
				/>
				<card
					v-for="(office, index) in offices"
					:office="office"
					:key="index"
					@save-office="office => saveOffice(office, index)"
					@delete-office="deleteOffice(index)"
				/>
				<div class="text-center">
					<p class="text-grey-200">This project is for test purpose only.</p>
					<a
						href="www.dogandponystudios.com"
						class="block text-blue text-xs uppercase mt-2"
					>
						www.dogandponystudios.com
					</a>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import offices from "~/data/offices.json";

import Card from "~/components/Card";
import FormComponent from "~/components/Form";
import Plus from "~/icons/Plus";
import Check from "~/icons/Check";
import Times from "~/icons/Times";

export default {
	components: {
		Card,
		FormComponent,
		Plus,
		Check,
		Times
	},
	data() {
		return {
			offices,
			editedTab: null,
			addForm: false,
			notificationActive: false,
			form: {
				color: "yellow",
				title: "",
				address: "",
				name: "",
				position: "",
				email: "",
				phone: ""
			}
		};
	},
	methods: {
		addOffice(office) {
			this.offices.unshift({ ...office });
			this.addForm = false;
		},
		saveOffice(office, index) {
			this.offices[index] = office;
			this.notificationActive = true;
		},
		deleteOffice(index) {
			this.notificationActive = false;
			this.offices[index] = {};
			this.notificationActive = true;
			setTimeout(() => {
				this.notificationActive = false;
			}, 5000);
		}
	}
};
</script>

<style>
html,
body,
#__nuxt,
#__layout {
	min-height: 100%;
	@apply h-full;
}

.card-base {
	@apply relative;
	@apply px-6;
	@apply rounded-lg;
	@apply shadow-base;
}

.content-wrapper {
	max-width: 318px;
}
</style>
