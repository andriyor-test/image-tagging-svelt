<script>	
	import nanoid from "nanoid/non-secure";
	import { taggs } from "./tags.js";

	let tags = [...taggs];
	let current;
	const TAG_ITEM_HALF_WIDTH = 76;
	const TAG_ITEM_HALF_HEIGHT = 15.5;

	function unsetCurrent() {
		current = null;
	}

	function setCurrent(tagIndex) {
		current = tagIndex;
	}

	function deleteTag(e, id) {
		e.stopPropagation();
		tags = tags.filter(tag => id != tag.id);
		unsetCurrent();
	}

	function addNewTag(e) {
		const image = document.getElementsByClassName('image')[0];
		tags = [...tags, {
			id: nanoid(),
			title: 'lorem',
			left: e.clientX - image.offsetLeft - TAG_ITEM_HALF_WIDTH,
			top:  e.clientY - TAG_ITEM_HALF_HEIGHT
		}];
		setCurrent(tags.length - 1);
	}

	function selectTag(e, tagIndex) {
		e.stopPropagation();
		setCurrent(tagIndex);
	}

	function getCoordinate(e, imageSize, elementRef) {
		const elementSize =elementRef.getBoundingClientRect();
		const coordinate = {
			x: 0, 
			y: 0
		};

		if (e.clientX > imageSize.left + imageSize.width - elementSize.width / 2) {
			coordinate.x = imageSize.width - elementSize.width
		} else if (e.clientX < imageSize.left + elementSize.width / 2) {
			coordinate.x = 0
		} else {
			coordinate.x = parseFloat(elementRef.style.left) + e.clientX - (elementSize.left + elementSize.width / 2)
		}

		if (e.clientY > imageSize.top + imageSize.height - elementSize.height / 2) {
			coordinate.y = imageSize.height - elementSize.height
		} else if (e.clientY < imageSize.top + elementSize.height / 2) {
			coordinate.y = 0
		} else {
			coordinate.y = parseFloat(elementRef.style.top) + e.clientY - (elementSize.top + elementSize.height / 2)
		}
		return coordinate;
}

	function dragEnd(e) {
		if (current !== null && current !== undefined) {
			const imageSize = document.getElementsByClassName('image')[0].getBoundingClientRect();
			const elementRef = document.getElementsByClassName('tagging-element')[current];
			
			const newCoordinate = getCoordinate(e, imageSize, elementRef);
			tags[current] = {...tags[current], ...{left: newCoordinate.x, top: newCoordinate.y}}
		}
	}
</script>


<div class="image" on:click="{(e) => addNewTag(e)}">
	{#each tags as tag, tagIndex}
		<div
			draggable="true"
			class="tagging-element {current === tagIndex ? 'grab-mode' : ''}"
			on:click="{(e) => selectTag(e, tagIndex)}"
			on:dragend="{(e) => dragEnd(e)}"
			style='z-index: 0; left: {tag.left}px; top: {tag.top}px;'
		>
			<div class="tagging-title">{tag.title}</div>
			<div class="delete {current === tagIndex ? '' : 'hide'}"
					on:click="{(e) => deleteTag(e, tag.id)}"
			>
				X
			</div>
		</div>
	{/each}
</div>


<style>
	.image {
		background-image: url(/images/image.jpg);
		width: 512px;
		height: 512px;
		position: relative;
		overflow: hidden;
		background-repeat: no-repeat;
		margin: 0 auto;
	}

	.tagging-element {
		position: absolute;
		border-radius: 5px;
		background-color: rgba(34,34,34,.75);
		opacity: .9;
		border: 1px solid #000;
		cursor: pointer;
		color: white;
		display: flex;
		flex-direction: row;
	}

	.tagging-title {
		padding: 5px 15px;
		white-space: nowrap;
		text-overflow: ellipsis;
    width: 100px;
    overflow: hidden;
	}

	.delete {
		padding: 5px;
	}

	.delete:hover {
		cursor: grabbing;
	}

	.hide {
		visibility: hidden;
	}

	.grab-mode {
		z-index: 16777270!important;
		cursor: move;
	}
</style>