<script>	
	import nanoid from "nanoid/non-secure";
	let current;
	const TAG_ITEM_HALF_WIDTH = 76;
	const TAG_ITEM_HALF_HEIGHT = 15.5;

	let tags = [
		{
			id: nanoid(),
			title: 'Lorem Ipsum',
			left: 150,
			top: 150,
		},
		{
			id: nanoid(),
			title: 'Lorem',
			left: 200,
			top: 300,
		},
		{
			id: nanoid(),
			title: 'Lorem ipsum dolor sit amet, asaa ASASASDasd  asdasd',
			left: 100,
			top: 100,
		}   
	];

	function unsetCurrent() {
		current = null;
	}

	function setCurrent(index) {
		current = index;
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

	function selectTag(e, index) {
		e.stopPropagation();
		setCurrent(index);
	}

	function dragEnd(e) {
		if (current !== null) {
			let elementRef = document.getElementsByClassName('tagging-element')[current];
			let elementSize =elementRef.getBoundingClientRect();
			let image = document.getElementsByClassName('image')[0].getBoundingClientRect();
			tags[current] = {...tags[current], 
			...{
					left: parseFloat(elementRef.style.left) + e.clientX - (elementSize.left + elementSize.width / 2), 
					top: parseFloat(elementRef.style.top) + e.clientY - (elementSize.top + elementSize.height / 2)
				}
			}
		}
	}
</script>


<div class="image" on:click="{(e) => addNewTag(e)}">
	{#each tags as tag, i}
		<div
			draggable="true"
			class="tagging-element {current === i ? 'grab-mode' : ''}"
			on:click="{(e) => selectTag(e, i)}"
			on:dragend="{(e) => dragEnd(e)}"
			style='z-index: 0; left: {tag.left}px; top: {tag.top}px;'
		>
			<div class="tagging-title">{tag.title}</div>
			<div class="delete {current === i ? '' : 'hide'}"
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
		top: -1px;
		padding: 5px;
	}

	.hide {
		visibility: hidden;
	}

	.grab-mode {
		z-index: 16777270!important;
		cursor: grab;
	}
</style>