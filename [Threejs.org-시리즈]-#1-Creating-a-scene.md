[원본 링크](https://threejs.org/docs/index.html#Manual/Introduction/Creating_a_scene)
^Threejs-introduction^

# Creating a Scene (Scene 만들기)

회전하는 큐브로 Scene을 설정하는 것으로부터 시작해보도록 하겠습니다. 
실제 예제는 페이지의 밑에 제공되므로, 먄약에 막히거나 도움이 필요하신 경우 참고하세요.

### 시작하기 전에

three.js를 시작하기 전에 먼저 그릴 장소가 필요합니다. 다음의 HTML을 컴퓨터에 저장하세요. [three.js](https://threejs.org/build/three.js)도 마찬가지로 `js/` 디렉토리에 저장하세요. 
완료하셨다면 브라우저에셔 (html파일을) 엽니다.
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset=utf-8>
		<title>My first three.js app</title>
		<style>
			body { margin: 0; }
			canvas { width: 100%; height: 100% }
		</style>
	</head>
	<body>
		<script src="js/three.js"></script>
		<script>
			// Our Javascript will go here.
		</script>
	</body>
</html>
```

### Scene 만들기

three.js로 무언가를 그리기 전에 필요한 것이 세 가지 있습니다: Scene, camera, renderer. 이를 통해 Scene을 camera로 render할 수 있습니다.
```
var scene = new THREE.Scene();
var camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000 );

var renderer = new THREE.WebGLRenderer();
renderer.setSize( window.innerWidth, window.innerHeight );
document.body.appendChild( renderer.domElement );
```
잠깐 여기서 무엇이 일어나고 있는지 이야기하고 넘어갑시다. 여기서 scene과 camera 그리고 rendere를 만들었습니다. three.js에는 여러가지 camera가 있는데 우선은 PerpectiveCamer를 사용합시다. 가장 첫번째 속성은 field of view~시야각~입니다.

두번째 속성은 aspect ratio~가로세로비율~입니다. 대부분의 경우 element의 가로를 세로로 나눈 값을 이용합니다. 만약에 이렇게 하지 않는다면 옛날 영화를 와이드스크린 TV에서 트는 듯한 효과를 얻을 것입니다.(즉, 이미지가 뭉개질 수 있습니다.)

그 다음 두가지 속성은 clippling plane의 near 와 far 값입니다. 물체가 카메라로부터 far 보다 멀때 또는 near 보다 가까울 때, render 되지 않습니다. 당장은 이것에 대해 걱정할 필요는 없습니다. 하지만 추후에 앱의 퍼포먼스를 향상시키기 위해서 다른 값을 넣어야 할 수도 있습니다.

다음은 renderer입니다. 여기가 바로 놀라운 일이 벌어지는 곳입니다. three.js는 여기서 WebGLRenderer를 제공함과 동시에, WebGL을 지원하지 않는 옛날 브라우저들을 위해 몇가지  fallback을 제공합니다.

renderer 인스턴스를 만드는 것과 함께 앱에 얼마만큼의 크기로 render하 것인지 정해야 합니다. 앱이 표시하는 곳의 가로와 세로 값을 이용하는 것이 일반적이므로, 여기서는 브라우저 창의 가로와 세로 값을 사용하겠습니다. 퍼포먼스에 민감한 앱의 경우 setSize를 이용하여 보다 작은 값을 줄 수도 있습니다. 예를 들어 window.innerWidth/2 나 window.innerHeight/2 를 이용하면 앱의 크기를 반으로 줄일 수 있습니다.

만약 앱의 크기를 그대로 하되 해상도만 낮게 render 하고 싶다면 setSize를 updateStyle에서 false로 만들어주면 됩니다. 예를 들어 `<canvas>`의 가로 세로를 100%로 해놓았다는 가정하에, `setSize(window.innerWidth/2, window.innerHeight/2, false)` 는 앱을 1/2 해상도로 render할 것입니다.

그리고 마지막으로, renderer element를 HTML에 넣습니다. 이것은 `<canvas>` element로서, renderer가 scene을 표시하는데 사용합니다.

	"그래 좋아. 그러면 도대체 그놈의 cube는 어디있는거지?"

자, 지금 만들어봅시다.

```
var geometry = new THREE.BoxGeometry( 1, 1, 1 );
var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
var cube = new THREE.Mesh( geometry, material );
scene.add( cube );

camera.position.z = 5;
```

큐브를 만들기 위해서는 `BoxGeometry`가 필요합니다. 이것은 cube의 모든 point(vertices~정점~)와 fill(faces~면~)을 지닌 object 입니다. 향후 조금더 알아볼 기회가 있을 것입니다.

도형과 함께 이를 색칠할 material이 필요합니다. Three.js 는 여러가지 material과 함께 제공되는데, 우선 여기선 MeshBasicMaterial로 진행하겠습니다. 모든 material은 자신에게 적용 될 속성값을 지닌 object를 받습니다. 간단하게 하기 위해서 일단 color값 0x00ff00만 적용하겠습니다. 이 색은 초록색입니다. 이 방식은 CSS나 photoshop에서 작용하는 방식과 같습니다 (hex colors).

세 번째로 필요로 하는 것은 Mesh입니다. Mesh는 geometry를 받아서 material을 입히는 object입니다. 이것을 이제 scene에 넣어서 자유롭게 움직일 수 있습니다.

Default~기본설정~으로 scene.add()를 호출하면 그것은 좌표상 (0,0,0)의 위치에 더해집니다. 이것은 camera와 cube가 서로 겹치게 만들므로, 이를 피하기 위해 camera의 위치를 옆으로 약간 조정합니다.

### Scene 렌더링하기

만약에 위에서 code를 그대로 복사해서 위 HTML파일에 붙여넣었다면, 아직 아무것도 보이지 않을 것입니다. 이것은 아직 실제로 아무것도 render하지 않았기 때문입니다. 따라서 **render loop** 이 필요합니다.

```
function render() {
	requestAnimationFrame( render );
	renderer.render( scene, camera );
}
render();
```

loop을 만들어 renderer가 scene을 일초당 60번 그리도록 만듭니다. 만약에 당신이 브라우저에서 게임을 만드는 것이 처음이라면 이렇게 물어볼수도 있습니다. 
	"왜 그냥 **setInterval**을 사용하지 않는거지?"
실제로, 그렇게 할 수 있습니다. 하지만 **requestAnimationFrame**은 몇가지의 장점이 있습니다. 그 중 가장 중요한 거라면 사용자가 다른 브라우저 탭을 사용하게 되면 일시적으로 정지한다는 점입니다. 따라서 프로세싱-파워와 배터리-파워를 절약할 수 있습니다.

### 큐브 Animate
여기까지 따라왔다면 green box를 볼 수 있습니다. 이제 조금 더 흥미롭게 만들어 봅시다.

다음 코드를 render function의 renderer.render 바로 위에다가 붙여넣습니다.
```
cube.rotation.x += 0.1;
cube.rotation.y += 0.1;
```
이것은 매 frame마다 작동하므로 (1초에 60번) 큐브에 부드러운 회전 animation을 줄 것입니다. 기본적으로, 앱이 작동할 때 무언가를 움직이거나 바꾸고 싶다면 이 render loop에 넣어야 합니다. 물론 여기에 다른 function을 넣을  수도 있습니다. 이는 render function이 수백줄이 안되도록 막아줍니다.

### 결과

축하합니다! 당신은 이제 방금 첫 three.js 어플리케이션을 만들었습니다.

전체 코드는 밑에 있습니다. 좀 더 가지고 놀면서, 이것이 어떻게 작동하는지 알아보세요.

```
<html>
	<head>
		<title>My first three.js app</title>
		<style>
			body { margin: 0; }
			canvas { width: 100%; height: 100% }
		</style>
	</head>
	<body>
		<script src="js/three.js"></script>
		<script>
			var scene = new THREE.Scene();
			var camera = new THREE.PerspectiveCamera( 75, window.innerWidth/window.innerHeight, 0.1, 1000 );

			var renderer = new THREE.WebGLRenderer();
			renderer.setSize( window.innerWidth, window.innerHeight );
			document.body.appendChild( renderer.domElement );

			var geometry = new THREE.BoxGeometry( 1, 1, 1 );
			var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
			var cube = new THREE.Mesh( geometry, material );
			scene.add( cube );

			camera.position.z = 5;

			var render = function () {
				requestAnimationFrame( render );

				cube.rotation.x += 0.1;
				cube.rotation.y += 0.1;

				renderer.render(scene, camera);
			};

			render();
		</script>
	</body>
</html>
```

