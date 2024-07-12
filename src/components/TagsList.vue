<template>
	<div
		class="tags-list__container"
		:class="{ 'tags-list__container--justified': justified }"
	>
		<div
			v-for="(item, index) in visibleTags"
			:key="index"
			class="tags-list__item"
		>
			<template v-if="item.type === 'tag'">
				<span class="tags-list__text">{{ item.text }}</span>
				<v-icon v-if="item.icon" class="tags-list__icon" size="16">
					mdi-{{ item.icon }}
				</v-icon>
			</template>
			<template v-else-if="item.type === 'separator'">
				<v-icon class="tags-list__separator" size="16">
					mdi-circle-small
				</v-icon>
			</template>
		</div>
	</div>
</template>

<script>
export default {
	name: 'TagsList',
	props: {
		tags: {
			type: Array,
			required: true,
		},
		justified: {
			type: Boolean,
			default: false,
		},
	},
	data() {
		return {
			visibleTags: [],
		}
	},
	mounted() {
		this.updateVisibleTags()
		window.addEventListener('resize', this.updateVisibleTags)
	},
	beforeDestroy() {
		window.removeEventListener('resize', this.updateVisibleTags)
	},
	methods: {
		updateVisibleTags() {
			this.visibleTags = []
			let containerWidth = this.$el.offsetWidth
			let currentWidth = 0
			let marginRight = 8 // Отступ справа, определенный в CSS

			for (let i = 0; i < this.tags.length; i++) {
				const item = this.tags[i]

				// Проверяем, является ли текущий элемент тегом
				if ('text' in item) {
					const tagWidth = this.getTagWidth(item.text)

					// Проверяем, поместится ли текущий тег + разделитель (если есть) + отступ
					if (
						currentWidth +
							tagWidth +
							(i < this.tags.length - 1 ? 16 : 0) +
							marginRight <
						containerWidth
					) {
						this.visibleTags.push({
							type: 'tag',
							text: item.text,
							icon: item.icon,
						})
						currentWidth += tagWidth

						// Если это не последний тег, добавляем разделитель
						if (i < this.tags.length - 1) {
							this.visibleTags.push({ type: 'separator' })
							currentWidth += 16 + marginRight
						}
					} else {
						// Если не помещается, останавливаем цикл
						break
					}
				} else {
					// Если это разделитель, добавляем его только если это не последний элемент
					if (i < this.tags.length - 1) {
						this.visibleTags.push({ type: 'separator' })
						currentWidth += 16 + marginRight
					}
				}
			}

			// Если последний элемент в visibleTags - разделитель, удаляем его
			if (
				this.visibleTags.length > 0 &&
				this.visibleTags[this.visibleTags.length - 1].type === 'separator'
			) {
				this.visibleTags.pop()
			}
		},
		getTagWidth(text) {
			const tagElement = document.createElement('span')
			tagElement.textContent = text
			tagElement.style.display = 'inline-block'
			document.body.appendChild(tagElement)
			const tagWidth = tagElement.offsetWidth
			document.body.removeChild(tagElement)
			return tagWidth
		},
	},
}
</script>

<style lang="scss">
.tags-list__container {
	display: flex;
	flex-wrap: wrap;
	align-items: center;
	&__item {
		display: flex;
		align-items: center;
		margin-right: 8px;
		.tags-list__text {
			margin-right: 8px;
		}
		.tags-list__icon {
			margin-right: 4px;
		}
	}
	&__separator {
		margin-left: 8px; // Отступ слева
	}
	&--justified {
		justify-content: space-between;
		.tags-list__item:not(:last-child) {
			margin-right: 0;
		}
		.tags-list__separator {
			margin-left: 0; // Убираем отступ слева
		}
	}
}
</style>
