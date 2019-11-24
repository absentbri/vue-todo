<template>
	<v-app id="inspire">
		<v-app-bar
			app
			color="indigo"
			dark
		>
			<v-toolbar-title>To-do List</v-toolbar-title>
		</v-app-bar>

		<v-content>
			<v-container fluid>
				<v-list
					two-line
					subheader
				>
					<v-container>

						<v-flex
							xs12
							justify-content="end"
						>
							<b>{{todos.length}}</b> Tasks
						</v-flex>

						<v-flex xs12>
							<v-text-field
								clearable
								v-model="newItem"
								label="Type your task"
								@keyup.enter="addNew"
							>
							</v-text-field>
						</v-flex>
					</v-container>

					<v-subheader class="subheading" v-if="!todos.length">You have no Tasks, add some</v-subheader>
					<v-subheader class="subheading" v-else>Your Tasks</v-subheader>

					<v-list-item
						v-for="(item, idx) in todos"
						:key="idx"
					>
						<template v-slot:default>
							<v-list-item-action>
								<v-checkbox
									v-model="item.done"
									color="primary"
								></v-checkbox>
							</v-list-item-action>

							<v-list-item-content>
								<v-list-item-title
									:class="{'done': item.done}"
								>
									{{ item.title }}
								</v-list-item-title>
								<v-list-item-subtitle>
									Created on: {{ formattedDate(item.created) }}
								</v-list-item-subtitle>
							</v-list-item-content>
						</template>
					</v-list-item>

				</v-list>
			</v-container>
		</v-content>

		<v-footer
			color="indigo"
			app
		>
			<span class="white--text">&copy; 2019</span>
		</v-footer>
	</v-app>
</template>

<script>
	import moment from 'moment'
	import axios from 'axios'

	export default {
		data: () => ({
			newItem: "",
			todos: []
		}),
		methods: {
			addNew() {
				const title = this.newItem.trim();
				if(title.length) {
					this.todos.push({
							title,
							created: moment().toDate(),
							done: false
					});
				}
				this.newItem = "";
			},
			formattedDate(date) {
				const dateObj = moment(date);
				return `${dateObj.format("dddd, D MMMM YYYY")} at ${dateObj.format("h:mm A")}`
			},
			put() {
				// CRUD: Create
				return axios.put(`http://localhost:8080/todos`)
					.then((response) => {
						console.log(response);
					})
					.catch((error) => {
						console.error(error);
					})
			},
			get() {
				// CRUD: Read
				return axios.get(`http://localhost:8080/todos`)
					.then((response) => {
						console.log(response);
					})
					.catch((error) => {
						console.error(error);
					})
			},
			post(id, done) {
				// CRUD: Update
				return axios.post(`http://localhost:8080/todos/${id}`, {
						data: {
							done
						}
					})
					.then((response) => {
						console.log(response);
					})
					.catch((error) => {
						console.error(error);
					})
			},
			delete() {
				// CRUD: Delete
				return axios.delete(`http://localhost:8080/todos/`)
					.then((response) => {
						console.log(response);
					})
					.catch((error) => {
						console.error(error);
					})
			}
		},
		created() {
			// this.get();
		}
	}
</script>

<style>
	.done {
		text-decoration: line-through;
	}
</style>
