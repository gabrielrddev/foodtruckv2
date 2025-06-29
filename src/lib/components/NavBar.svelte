<script>
	import { goto } from '$app/navigation';
	function pageSelect(page) {
		switch (page) {
			case 1:
				goto('/admin/foodList');
				break;
			case 2:
				goto('/admin/tablesList');
				break;
			case 3:
				goto('/admin/qrusers');
				break;
			case 4:
				goto('/admin/login');
				break;
		}
	}
</script>

<div class="bg-white shadow-sm border-b border-gray-200">
	<nav class="max-w-4xl mx-auto px-4 py-4">
		<ul class="flex justify-center space-x-4">
			<li>
				<button 
					onclick={() => pageSelect(1)} 
					class="px-4 py-2 rounded-md text-gray-700 hover:text-red-600 hover:bg-red-50 transition"
				>
					Pedidos
				</button>
			</li>
			<li>
				<button 
					onclick={() => pageSelect(2)} 
					class="px-4 py-2 rounded-md text-gray-700 hover:text-red-600 hover:bg-red-50 transition"
				>
					Mesas
				</button>
			</li>
			<li>
				<button 
					onclick={() => pageSelect(3)} 
					class="px-4 py-2 rounded-md text-gray-700 hover:text-red-600 hover:bg-red-50 transition"
				>
					Garçom
				</button>
			</li>
			<li>
				<button 
					onclick={() => pageSelect(4)} 
					class="px-4 py-2 rounded-md text-red-500 border border-red-500 hover:bg-red-50 transition font-medium"
				>
					Logout
				</button>
			</li>
		</ul>
	</nav>
</div>
