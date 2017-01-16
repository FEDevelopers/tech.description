# 번역 진행중입니다

# (introduction) Scene 만들기

이 섹션은 three.js에 대한 간단한 소개를 하기 위함입니다. 회전하는 큐브로 Scene을 설정하는 것으로부터 시작해보도록 하겠습니다. 실제 예제는 페이지의 밑에 제공되므로, 먄약에 막히거나 도움이 필요하신 경우 참고해주세요.

### 시작하기 전에

three.js를 시작하기 전에 먼저 이를 그릴 곳이 필요합니다. 다음의 HTML을 파일로 컴퓨터에 저장해주세요. [three.js](https://threejs.org/build/three.js)도 마찬가지로 `js/` 디렉토리에 저장해주세요. 완료하셨다면 브라우저에셔 (html파일을) 열어주세요.
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




