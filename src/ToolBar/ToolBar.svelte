<script>				
	import { createEventDispatcher} from 'svelte/internal'

	import ColorPicker from './ColorPicker.svelte'
	import CodeIcon from '../Icons/CodeIcon.svelte'
	
	import LinkInput from './LinkInput.svelte'
	import CenterIcon from '../Icons/CenterIcon.svelte'
	import LeftIcon from '../Icons/LeftIcon.svelte'
	import RightIcon from '../Icons/RightIcon.svelte'
	import JustifyIcon from '../Icons/JustifyIcon.svelte'
	import Leading from './Leading.svelte'
	import HeadingList from './HeadingList.svelte'
	
	export let setClass
	export let setGClass
	export let classes
	export let g_classes
	export let href
	// export let setLink
	export let base_node
	let dispatch = createEventDispatcher()
	export let mouseX
	
	function firstParentRelative(n){
		while(n.parentNode && n.parentNode.tagName){
			n = n.parentNode
			if(window.getComputedStyle(n).getPropertyValue('position').toLowerCase() == 'relative'){
				return n.getBoundingClientRect()
			}
		}
		return {top: -window.scrollY, left: 0}
	}

	function setPosition(node){
		let e = window.event;
		if(!base_node) return
		
		let elm = base_node.parentNode.tagName == 'DIV' ? base_node : base_node.parentNode
		let rect = elm.parentNode.getBoundingClientRect()
		let posY = rect.top-10; 
		if(elm.previousElementSibling){
			let ch_nodes = [...elm.parentNode.childNodes]
			let siblings = ch_nodes.slice(0,ch_nodes.indexOf(elm)+1)
			let br = siblings.reverse().find(elm => elm.tagName == 'BR')
			if(br){
				rect = br.getBoundingClientRect()
				posY = rect.top ? rect.top : posY
			}
		}

		let rel_rect = firstParentRelative(node)
		let pos_top = posY-rel_rect.top
		if(posY<30)
			pos_top = 30
		node.style.top = `${pos_top}px`
		mouseX = mouseX || 10
		let mx = mouseX-node.offsetWidth/2 
		mx = mx > 0 ? mx : 10
		mx = mouseX+node.offsetWidth/2 < window.innerWidth ? mx : window.innerWidth-node.offsetWidth
		node.style.left = `${mx-rel_rect.left}px`
	}

	function close(){
		dispatch('close')
	}

	function cexist(klass){
		return classes.includes(klass)
	}
	function cgexist(klass){
		return g_classes.includes(klass)
	}

	const STYLE = {
		BOLD: 'font-bold',
		ITALIC: 'italic',
		UNDERLINE: 'underline',
		LINETHROUGH: 'line-through',
		CODE: 'font-mono px-2 border border-gray-200',
		LINK: 'underline text-blue-500 link',
		CENTER: 'text-center',
		LEFT: 'text-left',
		RIGHT: 'text-right',
		JUSTIFY: 'text-justify'
	}

	let e_classes = {}

	function initEClasses(){
		e_classes = {
			bold: cexist(STYLE.BOLD),
			italic: cexist(STYLE.ITALIC),
			underline: cexist(STYLE.UNDERLINE),
			linethrough: cexist(STYLE.LINETHROUGH),
			code: cexist(STYLE.CODE),
			link: cexist(STYLE.LINK),
			justify: cgexist(STYLE.JUSTIFY),
			center: cgexist(STYLE.CENTER),
			left: cgexist(STYLE.LEFT),
			right: cgexist(STYLE.RIGHT),
			
		}
	}
	initEClasses()

	function toggle(klass){
		setClass(klass)
		if(!classes.includes(klass)){
			classes = classes.split(' ').concat([klass]).join(' ')
		}else{
			let n_classes  = classes.split(' ')
			n_classes.splice(n_classes.indexOf(klass),1)
			classes = n_classes.join(' ')
		}
		
		initEClasses()
	}
	
	
	// duplicated in contenteditor
	function replaceGClass(klass, reg, gklass){
		let classes = gklass.split(' ')
		let s_index = classes.findIndex(c => reg.test(c))
		let selected_class = ~s_index ? classes[s_index] : ''
		if(selected_class){
			gklass = gklass.replace(selected_class,'').trim()
		}
		gklass = gklass.split(' ').concat([klass]).join(' ')
		
		return gklass
	}

	// duplicated!! 
	const reg_position = /^text\-(left|right|center)/


	function toggleG(klass){
		setGClass(klass)
		if(reg_position.test(klass)){
			g_classes = replaceGClass(klass,reg_position,g_classes)
		}else if(!g_classes.includes(klass)){
			g_classes = g_classes.split(' ').concat([klass]).join(' ')
		}else{
			let n_classes  = g_classes.split(' ')
			n_classes.splice(n_classes.indexOf(klass),1)
			g_classes = n_classes.join(' ')
		}
		
		initEClasses()
	}
	
</script>

<div use:setPosition on:mousedown|stopPropagation class="flex absolute font-normal -mt-6 shadow bg-white z-50 text-base rounded">	
	<div class="rounded flex items-center shadow-lg border border-gray-200  text-gray-700">
			<div class="border-r">
				<HeadingList setClass={setGClass} klass={g_classes} />	
			</div>
			<div class="px-2 cursor-pointer select-none { e_classes.bold ? 'text-blue-600':''} font-medium hover:bg-gray-200 py-1" on:mousedown={() => toggle(STYLE.BOLD)}>
				B
			</div>
			<div class="px-3 cursor-pointer select-none { e_classes.italic ? 'text-blue-600':''} italic hover:bg-gray-200 py-1" on:mousedown={() => toggle(STYLE.ITALIC)}>
				i
			</div>
			<div class="px-2 cursor-pointer select-none { e_classes.underline ? 'text-blue-600':''} underline hover:bg-gray-200 py-1" on:mousedown={() => toggle(STYLE.UNDERLINE)}>
				U
			</div>
			<div class="px-2 cursor-pointer select-none { e_classes.linethrough ? 'text-blue-600':''} line-through hover:bg-gray-200 py-1" on:mousedown={() => toggle(STYLE.LINETHROUGH)}>
				S
			</div>
			<div class="px-2 cursor-pointer select-none { e_classes.code ? 'text-blue-600':''} line-through hover:bg-gray-200 py-2" on:mousedown={() => toggle(STYLE.CODE)}>
				<CodeIcon />
			</div>
			<div class="{ e_classes.link ? 'text-blue-600':''} cursor-pointer select-none hover:bg-gray-200 text-sm border-r">
				<LinkInput setLink={setClass} on:close={close} {href} />
			</div>
			
			<div class="pl-1 cursor-pointer select-none hover:bg-gray-200 py-1 ">
				<ColorPicker {setClass} klass={classes} />
			</div>
			<div class="px-1 cursor-pointer select-none hover:bg-gray-200 border-r h-full flex items-center">
				<ColorPicker txt="bg" {setClass} klass={classes} />
			</div>
		
			<div class="px-2 { e_classes.justify ? 'text-blue-600':'text-gray-700'} cursor-pointer select-none hover:bg-gray-200 py-1 h-full flex items-center" on:mousedown={() => toggleG(STYLE.JUSTIFY)}>
				<JustifyIcon />
			</div>
			
			<div class="px-2 { e_classes.left ? 'text-blue-600':'text-gray-700'} cursor-pointer select-none hover:bg-gray-200 py-1 h-full flex items-center"  on:mousedown={() => toggleG(STYLE.LEFT)}>
				<LeftIcon />
			</div>
			
			<div class="px-2 { e_classes.center ? 'text-blue-600':'text-gray-700'} cursor-pointer select-none hover:bg-gray-200 py-1 h-full flex items-center" on:mousedown={() => toggleG(STYLE.CENTER)}>
				<CenterIcon />
			</div>
			
			<div class="px-2 { e_classes.right ? 'text-blue-600':'text-gray-700'} cursor-pointer select-none hover:bg-gray-200 py-1 h-full flex items-center border-r"  on:mousedown={() => toggleG(STYLE.RIGHT)}>
				<RightIcon />
			</div>
		
			<div class="cursor-pointer select-none hover:bg-gray-200 h-full flex items-center">
				<Leading setClass={setGClass} klass={g_classes} />
			</div>
			
			
	</div>
</div>


