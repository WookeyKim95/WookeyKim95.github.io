---
layout: post
title:  "[DirectX] Normal Texture의 사용"
subtitle:  
date: 2023-01-04 19:03:03 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] Normal Texture의 사용<br/>
<br/>

## png파일에 법선벡터가 들어간 경우<br/>
<Br/>

png파일에 방향정보가 들어가있는 경우도 있다.<br/>

정확히 말하면 법선벡터 정보가 들어가있어서 이를 Directional Light효과로 사용할 수 있게 되는 것이다.<br/>

이를 어떻게 활용할 수 있을지 살펴보자.<br/>
<br/>

### CLevelMgr.cpp 파일<br/>
<br/>

새로운 재질을 설정하고 다른 텍스쳐를 불러오도록 설정한다.<br/>

그리고 pParent 텍스쳐를 시계방향으로 45도 회전시켜둔다.<br/>

```
// CLevelMgr.cpp 파일 중에서

```
<Br/>

### std2d.fx 파일<br/>
<br/>

```
float4 PS_Std2DLight(VS_Light_OUT _in) : SV_Target
{
    float4 vOutColor = (float4) 0.f;
       
    if (g_btex_0)
    {
        vOutColor = g_tex_0.Sample(g_sam_0, _in.vUV);
    }
    else
    {
        vOutColor = float4(1.f, 0.f, 1.f, 1.f);
    }

    float vNormal = (float3) vNormal;
    if (g_btex_1)
    {
        vNormal = g_tex_1.Sample(g_sam_0, _in.vUV);
    }

    if (0.f == vOutColor.a)
        discard;
```
<Br/>

### RGB 데이터에 방향 벡터 값을 넣으면 어떻게 될까?<br/>
<br/>

생각해보면 RGB는 각각 8비트의 값으로 구성이 된다.<br/>

하지만 법선벡터, 방향벡터의 값은 각각 실수인데, 어떻게 각각 8비트의 공간에 방향 정보를 담을 수 있는 걸까?<br/>

8비트로 표현이 가능한 가지수는 256개이다.<br/>

그리고 방향벡터를 정규화 시키면 한 칸당 범위값은 -1부터 1까지이다.<br/>

-1부터 1까지의 실수 범위를 256가지의 수로 표현을 하는 것이다.<br/>
<Br/>

### 셰이더에서 RGB에 들어있는 방향벡터 값을 사용하려면?<br/>
<Br/>

우리가 사용하고자 하는 범위는 0부터 1이지만 256가지의 수로 표현이 되는 범위는 -1부터 1까지로 총 범위는 2이다.<br/>

그래서 우리가 사용하고자 하는 범위 0부터 1까지를 두배로 늘린 0부터 2까지로 늘리고 -1만큼 이동시킨다.<br/>

그리고 Normal 벡터는 현재 쓰는 좌표계와 다른 좌표계를 쓴다.<br/>

y축과 z축이 서로 뒤바뀌어 있는 좌표계이다.<br/>

그래서 이 둘을 바꿔줘야 한다.<br/>

그리고 물체의 회전과 크기 상태에 맞춰서 vNormal 벡터도 바꿔줘야 한다.<br/>

```
    float vNormal = (float3) vNormal;
    if (g_btex_1)
    {
        // Normal 값 추출
        vNormal = g_tex_1.Sample(g_sam_0, _in.vUV);

        // 0 ~ 1범위를 -1 ~ 1로 변경
        vNormal = (vNormal * 2.f) - 1.f;

        // y축과 z축의 스왑
        float f = vNormal.y;
        vNormal.y = vNormal.z;
        vNormal.z = f;

        // Texture 에서 추출한 Normal 방향을 월드로 변환시킨다.
        vNoraml = normalize(mul(float4(vNormal, 0.f), g_matWorld)).xyz;
    }
```

그래서 코드를 위와 같이 작성할 수 있다.<br/>
<Br/>

## 램버트 코사인 법칙<br/>
<br/>

표면이 받는 빛의 세기를 램버트 코사인 법칙으로 계산할 수 있다.<br/>

빛이 진행하는 방향벡터에 음수를 붙인 값과 표면의 법선 벡터 사이 각의 값이 0에 가까울 수록 표면이 받는 빛의 세기가 강해진다.<br/>

그리고 사이 각의 값이 90도를 넘어가면, 즉 두 벡터 사이의 코사인 값이 음수가 되면 빛을 더이상 받지 않는다.<br/>
<Br/>

### func.fx 파일<br/>
<Br/>

그래서 func에서 두 벡터를 내적해서 세기를 구한다.<br/>

그리고 또 주의할점은 내적 값이 음수가 되면 중첩되어야할 빛이 오히려 뺏기는 경우가 발생하므로 범위를 0과 1사이로 조정하는 saturate처리를 해주어야 한다.<br/>

```
void CalcLight2D(float3 _vWorldPos, float3 _vWorldDir, inout tLightColor _Light)
{
    for (int i = 0; i < iLightCount; ++i)
    {
        if (arrInfo[i].LightType == 0)
        {
            // 방향광일 때

            // 두 벡터의 내적
            float fDiffusePow = saturate(dot(-arrInfo[i].vWorldDir.xyz, _vWorldDir));
            
            _Light.vDiffuse.rgb += arrInfo[i].Color.vDiffuse.rgb * fDiffusePow;
            _Light.vAmbient.rgb += arrInfo[i].Color.vAmbient.rgb;
        }

        else if (arrInfo[i].LightType == 1)
        {
            float3 vLightWorldPos = float3(arrInfo[i].vWorldPos.xy, 0.f);
            float3 vWorldPos = float3(_vWorldPos.xy, 0.f);

            // 광원 중심에서 물체를 향하는 방향
            float3 vLight = normalize(vWorldPos - vLightWorldPos);
            float fDiffusePow = saturate(dot(-vLight, _vWorldDir));

            float fDistance = abs(distance(vWorldPos, vLightWorldPos));
            float fDistPow = saturate(1.f - (fDistance / arrInfo[i].Radius));
        
            _Light.Diffuse.rgb += arrInfo[i].Color.vDiffuse.rgb * fDiffusePow * fDistPow;
        }
    }   
}

```