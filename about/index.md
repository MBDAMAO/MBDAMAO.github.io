# About


{{< script >}}
function spawnConfettiAtClick(event) {confetti({origin: {
x: event.clientX / window.innerWidth,
y: event.clientY / window.innerHeight,
},});}
var script = document.createElement('script');
script.src = '/js/fireworks.js';
document.head.appendChild(script);
document.addEventListener("click", spawnConfettiAtClick);
{{< /script >}}
这里是 damao 的博客，有空会更新

