<script>	
	let current;
	let isMoved = false;

	let tags = [
		{
			id: 1,
			title: 'Lorem Ipsum',
			left: 150,
			top: 150,
		},
		{
			id: 2,
			title: 'Lorem',
			left: 200,
			top: 300,
		},
		{
			id: 3,
			title: 'Lorem ipsum dolor sit amet, asaa ASASASDasd  asdasd',
			left: 100,
			top: 100,
		}   
	]

	function deleteTag(id) {
		unsetCurrent()
		tags = tags.filter(tag => id != tag.id);
	}

	function unsetCurrent() {
		current = null
		changeCoordinate()
	}

	function changeCoordinate() {
		tags[0]  = {...tags[0], ...{left: 150, top: 150}}
	}

	function setCurrent(e, index) {
		e.stopPropagation()
		current = index	
	}

	function mousemoveListner(e){
		e.preventDefault()
		
		if (current && isMoved) {
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

	function mousedownListner(e){
		isMoved = true;
	}

	function mouseupListner(e) {
		isMoved = false;
	}
</script>


<div class="image" on:click="{() => unsetCurrent()}">
	{#each tags as tag, i}
		 	<div
		class="tagging-element {current === i ? 'grab-mode' : ''}"
		on:click="{(e) => setCurrent(e, i)}"
		on:mousemove="{(e) => mousemoveListner(e)}"
		on:mousedown="{(e) => mousedownListner(e)}"
		on:mouseup="{(e) => mouseupListner(e)}"
		style='z-index: 0; left: {tag.left}px; top: {tag.top}px;'
		
	>
		<div class="tagging-title">{tag.title}</div>
		<div class="delete {current === i ? '' : 'hide'}"
				on:click="{() => deleteTag(tag.id)}"
		>
			X
		</div>
	</div>
	{/each}
</div>


<style>
	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}


	.image {
		background-image: url(image.jpg);
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
		white-space: nowrap;
		text-overflow: ellipsis;
    /* width: 100px; */
    overflow: hidden;
	}

	.hide {
		visibility: hidden;
	}

	.grab-mode {
		z-index: 16777270!important;
		cursor: grab;
	}
</style>