> https://medium.com/outsystems-experts/beyond-memory-leaks-in-javascript-d27fd48ae67e#.grvvsvdwd 을 번역하였습니다.

***

메모리 누수는 졸업파티 밤에 찾아오는 당신의 코에 생긴 뾰루지와 같습니다. 당신은 그것들이 존재하고, 아무데도 가지 않는다는걸 알고있으며, 당신은 단지 그들이 확 올라오지 않거나 마술을 할 시간이 올 때 아무도 알아차리지 않기를 기도합니다.  
뾰루지들은 불편하고, 더럽습니다. 마치 거리에서 당신의 코를 세상에 알리려고 하는 신호처럼 느껴집니다. 당신이 원했던 것은 모두 당신의 스매싱 댄스 동작으로 신사 숙녀에게 인상을 주는것 뿐이었습니다. (레코딩을 위해 무도회 밤에는 여드름이 없었고 나는 챔피언처럼 춤을 췄습니다.)

![댄스타임](https://cdn-images-1.medium.com/max/800/1*UtFX9ErVYjtj6YXFf7vAuQ.gif)

[OutSystems](https://www.outsystems.com/)를 포함하여 모든 프론트-개발자들은 자바스크립트 누수를 한번 또는 여러번 겪었습니다. 이와 관련해서 다음 3가지 유형의 개발자가 있습니다:

1. 정통한 사람 : 그들이 무엇을 다루고 있는지를 알고 문제를 해결 한 사람들.
2. 인식하는 사람: 그들이 무엇을 다루는지를 알지만, 문제를 해결하지 못하는 사람들.
3. 관심없는 사람: 처음에는 문제가 있는지를 못느끼는 사람들

With this in mind, we have compiled a few guidelines to help those #3s leave the comfort of their oblivion and help #2s delve a little deeper. And, you never know, but those clever #1s may also enjoy, and hopefully endorse, some of these tips. We’re open for discussion, too. We’ve got treats for everyone!

