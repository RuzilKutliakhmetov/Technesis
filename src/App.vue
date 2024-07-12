<template>
	<div id="app">
		<TagsList :tags="tags" :justified="true" />
	</div>
</template>

<script>
import TagsList from './components/TagsList.vue'

export default {
	components: {
		TagsList,
	},
	data() {
		return {
			tags: [
				{ text: 'текст1' },
				{ text: 'текст2текст2текст2текст2текст2', icon: 'mdi-account' },
				{ text: 'текст3' },
				{ text: 'текст4', icon: 'mdi-calendar' },
				{ text: 'текст2текст2текст2текст2текст2' },
				{ text: 'текст6' },
				{ text: 'текст7' },
				{ text: 'текст8' },
			],
		}
	},
}
</script>
