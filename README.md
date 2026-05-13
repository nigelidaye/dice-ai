<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>骰子AI分析系统</title>
<script src="https://cdn.tailwindcss.com"></script>
</head>

<body class="bg-black text-white min-h-screen">

<div class="max-w-6xl mx-auto p-6">

<div class="flex flex-col lg:flex-row lg:items-center lg:justify-between gap-4 mb-8">
<div>
<h1 class="text-4xl font-bold">骰子 AI 数据分析系统</h1>
<p class="text-zinc-400 mt-2">
实时概率分析 · 连续走势监控 · AI偏态检测
</p>
</div>

<div class="flex gap-3">
<button onclick="addResult('大')" class="px-5 py-3 rounded-2xl bg-white text-black font-semibold">
录入“大”
</button>

<button onclick="addResult('小')" class="px-5 py-3 rounded-2xl bg-zinc-800 border border-zinc-700">
录入“小”
</button>

<button onclick="resetData()" class="px-5 py-3 rounded-2xl bg-red-500/20 border border-red-500/30 text-red-300">
清空数据
</button>
</div>
</div>

<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 mb-8">

<div class="bg-zinc-900 border border-zinc-800 rounded-2xl p-5">
<div class="text-zinc-400 text-sm">总局数</div>
<div id="total" class="text-3xl font-bold mt-2">0</div>
</div>

<div class="bg-zinc-900 border border-zinc-800 rounded-2xl p-5">
<div class="text-zinc-400 text-sm">大概率</div>
<div id="bigRate" class="text-3xl font-bold mt-2">0%</div>
</div>

<div class="bg-zinc-900 border border-zinc-800 rounded-2xl p-5">
<div class="text-zinc-400 text-sm">最长连大</div>
<div id="longBig" class="text-3xl font-bold mt-2">0</div>
</div>

<div class="bg-zinc-900 border border-zinc-800 rounded-2xl p-5">
<div class="text-zinc-400 text-sm">当前趋势</div>
<div id="trend" class="text-3xl font-bold mt-2">-</div>
</div>

</div>

<div class="bg-zinc-900 border border-zinc-800 rounded-2xl p-5">

<div class="flex items-center justify-between mb-4">
<h2 class="text-xl font-semibold">实时走势记录</h2>
<span class="text-zinc-400 text-sm">AI动态分析</span>
</div>

<div class="overflow-x-auto">
<table class="w-full text-left">
<thead>
<tr class="border-b border-zinc-800 text-zinc-400 text-sm">
<th class="pb-3">局数</th>
<th class="pb-3">结果</th>
<th class="pb-3">连续次数</th>
<th class="pb-3">偏态分析</th>
</tr>
</thead>

<tbody id="tableBody"></tbody>

</table>
</div>

</div>

</div>

<script>

let data = [];

function addResult(result) {

const last = data[data.length - 1];

let streak = 1;

if(last && last.result === result){
streak = last.streak + 1;
}

let bias = '正常';

if(streak >= 5){
bias = `极端偏${result}`;
}else if(streak >= 3){
bias = `偏${result}`;
}else if(streak >= 2){
bias = `轻偏${result}`;
}

data.push({
round:data.length + 1,
result,
streak,
bias
});

render();

}

function resetData(){
data = [];
render();
}

function render(){

const total = data.length;

const big = data.filter(x => x.result === '大').length;

const longestBig = Math.max(
0,
...data
.filter(x => x.result === '大')
.map(x => x.streak)
);

const trend = total
? data[data.length - 1].result
: '-';

document.getElementById('total').innerText = total;

document.getElementById('bigRate').innerText =
total
? Math.round(big / total * 100) + '%'
: '0%';

document.getElementById('longBig').innerText = longestBig;

document.getElementById('trend').innerText = trend;

const tbody = document.getElementById('tableBody');

tbody.innerHTML = '';

data.slice().reverse().forEach(item => {

tbody.innerHTML += `
<tr class="border-b border-zinc-800">

<td class="py-4">#${item.round}</td>

<td class="py-4">
<span class="px-3 py-1 rounded-full ${
item.result === '大'
? 'bg-emerald-500/20 text-emerald-400'
: 'bg-orange-500/20 text-orange-400'
}">
${item.result}
</span>
</td>

<td class="py-4">${item.streak}</td>

<td class="py-4 text-zinc-300">${item.bias}</td>

</tr>
`;

});

}

render();

</script>

</body>
</html>