---
layout: post
comments: true
categories: Computer-Graphics
---

> Being familiar with Qt framework and DirectX math library, I use them to implement ray tracing algorithm and render the picture onto the widget of Qt.

## Introduction

In computer graphics, ray tracing is a technique for generating an image by tracing the path of light through pixels in an image plane and simulating the effects of its encounters with virtual objects. The technique is capable of producing a very high degree of visual realism, usually higher than that of typical scanline rendering methods, but at a greater computational cost. This makes ray tracing best suited for applications where the image can be rendered slowly ahead of time, such as in still images and film and television visual effects, and more poorly suited for real-time applications like video games where speed is critical. Ray tracing is capable of simulating a wide variety of optical effects, such as reflection and refraction, scattering, and dispersion phenomena (such as chromatic aberration).

## Basic Idea

It works by tracing a path from an imaginary eye through each pixel in a virtual screen, and calculating the color of the object visible through it. Each ray must be tested for intersection with objects in the scene. Once the nearest object has been identified, the algorithm will estimate the incoming light at the point of intersection, examine the material properties of the object, and combine this information to calculate the final color of the pixel.

![idea](./idea.png)

## Implementation

* **Emit rays from each pixel**

In order to render each pixel of the widget, the function `paintEvent()` provided by Qt should be overwritten. Then I use `QPainter` to set the color of the pixels.

The resolution of the picture is set to 600 * 600. So I map [0, 600] to [-1, 1] and calculate out the ray vector. The function `EmitRay()` will return the color of its pixel.

{% highlight cpp %}
void RayTracingWidget::paintEvent(QPaintEvent* pEvent)
{
	for (int i = 0; i < m_width; i++)
	{
		float x = (i - 300) / 300.0;
		for (int j = 0; j < m_height; j++)
		{
			float y = -(j - 300) / 300.0;
			
			float tanResult = tanf(camera->FOV * 0.5 / 180 * XM_PI); // half of the width/height, s
			XMVECTOR right = camera->right * (x * tanResult); 
			XMVECTOR up = camera->up * (y * tanResult);
			Ray ray(camera->pos, XMVector3Normalize(camera->front + right + up));

			Color color = EmitRay(ray, 0);
			color.SetValid();
			QPainter painter(this);
			QColor qColor(color.color.m128_f32[0] * 255, color.color.m128_f32[1] * 255, color.color.m128_f32[2] * 255);
			painter.setPen(qColor);
			QPoint point(i, j);
			painter.drawPoint(point);
		}
	}
}
{% endhighlight %}

* **Recursive ray tracing**

I trace the rays recursively for `maxDepth` times. It is undesirable to trace the rays infinitely since there are possible situations that the rays never end, considering two mirrors reflecting each other. The colors should be modulated according to the `reflectiveness` of its material.

{% highlight cpp %}
Color RayTracingWidget::EmitRay(Ray& ray, int depth)
{
	for (int i = 0; i < 3; i++)
	{
		IntersectResult result;
		
		if (geometry[i]->IntersectPoint(ray, result))
		{
			Color color = geometry[i]->pMaterial->sample(ray, result.pos, result.normal);
			float reflectiveness = geometry[i]->pMaterial->reflectiveness;
			Ray reflectRay(result.pos, ray.dir - result.normal * 2 * (XMVector3Dot(ray.dir, result.normal).m128_f32[0]));
			if (depth + 1 <= maxDepth)
				return color * (1 - reflectiveness) + EmitRay(reflectRay, depth + 1) * reflectiveness;
			else
				return color;
		}
	}
	return BLACK;
}
{% endhighlight %}

* **Class design**

*classes designed for the objects in the scene:*
{% highlight cpp %}
// pure abstract class
class Geometry
{
public:
	XMVECTOR pos;
	Material* pMaterial;
	Geometry(XMVECTOR tPos, Material* tpMaterial)
	{
		pos = tPos;
		pMaterial = tpMaterial;
	}
	virtual bool IntersectPoint(Ray& ray, IntersectResult& result) = 0;
};

class Sphere : public Geometry
{
	// |VectorX - VectorOrigin| = r, equation of sphere
public:
	float radius;
	Sphere(XMVECTOR tPos, float tRadius, Material* tpMaterial) : Geometry(tPos, tpMaterial)
	{
		radius = tRadius;
	}

	// overwrite the pure abstract function
	bool IntersectPoint(Ray& ray, IntersectResult& result)
	{
		// calculate the intersection point of sphere and ray
		// ...
	}

};

class Plane : public Geometry
{
	// VectorX * VectorNormal = d, equation of plane
public:
	XMVECTOR normal;
	int distance;
	Plane(XMVECTOR tPos, XMVECTOR tNormal, int tDistance, Material* tpMaterial) : Geometry(tPos, tpMaterial)
	{
		normal = tNormal;
		distance = tDistance;
	}

	// overwrite the pure abstract function
	bool IntersectPoint(Ray& ray, IntersectResult& result)
	{
		// calculate the intersection point of plane and ray
		// ...
	}

};

{% endhighlight %}

*classes designed for the materials of the objects in the scene:*

{% highlight cpp %}

// pure abstract class
class Material
{
public:
	float reflectiveness;
	Material(float tReflectiveness)
	{
		reflectiveness = tReflectiveness;
	}

	virtual Color sample(Ray& ray, XMVECTOR& pos, XMVECTOR& normal) = 0;
};

class CheckerMaterial : public Material
{
	float scale;
public:
	CheckerMaterial(float tScale, float tReflectiveness) : Material(tReflectiveness)
	{
		scale = tScale;
	}
	
	// overwrite the pure abstract function
	Color sample(Ray& ray, XMVECTOR& pos, XMVECTOR& normal)
	{
		// calculate the color
		// ...
	}

};

class PhongMaterial : public Material
{
	XMVECTOR diffuse;
	XMVECTOR specular;
	float shininess;
	float reflectiveness;
	Light light;
public:
	PhongMaterial(XMVECTOR& tDiffuse, XMVECTOR& tSpecular, float tShininess, float tReflectiveness) : Material(tReflectiveness)
	{
		diffuse = tDiffuse;
		specular = tSpecular;
		shininess = tShininess;
	}

	// overwrite the pure abstract function
	Color sample(Ray& ray, XMVECTOR& pos, XMVECTOR& normal)
	{
		// calculate the color
		// ...
	}

};

{% endhighlight %}

## Exhibition

![raytracing](./raytracing.jpg)

Monte Carlo method to simulate color bleeding caused by diffuse reflection:

![raytracing](./raytracing2.jpg)