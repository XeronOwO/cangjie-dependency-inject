<div align="center">

# 仓颉依赖注入

[English](README.md) | **&gt; 简体中文 &lt;**  

</div>

## 简介

适配于仓颉语言的依赖注入模块，灵感源自 [Microsoft.Extensions.DependencyInjection](https://github.com/dotnet/runtime/tree/main/src/libraries/Microsoft.Extensions.DependencyInjection) 。  

## 使用样例

```cangjie
package app

import dependency_inject.*
import std.collection.*
import std.reflect.*
import std.unittest.*

interface I1 <: ToString {}

class C1 <: I1 {
	public func toString(): String { "C1" }
}

interface I2 <: ToString {}

class C2 <: I2 {
	private let _i1: I1
	public init(i1 : I1) {
		_i1 = i1
	}
	
	public func toString(): String { "C2(${_i1})" }
}

interface I3 <: ToString {}

class C3 <: I3 {
	private let _i1: I1
	private let _i2: I2
	public init(i1 : I1, i2 : I2) {
		_i1 = i1
		_i2 = i2
	}
	
	public func toString(): String { "C3(${_i1}, ${_i2})" }
}

@Test
main(): Unit {
	let services = ServiceCollection()
	ServiceCollection.addTransient2<I1, C1>(services)
	ServiceCollection.addTransient2<I2, C2>(services)
	ServiceCollection.addTransient2<I3, C3>(services)
	let provider = ServiceProvider(services, ServiceProviderOptions())
	let i3 = IServiceProvider.getRequiredService1<I3>(provider)
	@Expect(i3.toString() == "C3(C1, C2(C1))")
}

```

## 特别说明

由于仓颉的反射体系尚未完善，无法反射开放泛型类型、枚举类型，例如 `Array<>`、`Option<T>`，因此使用上会有所限制。  
目前只支持比较简单的 Interface-Implementation 关系注入，不支持诸如 `Iterable<T>` 等泛型的注入。  

## 特别感谢

特别感谢仓颉开发团队与仓颉鸿蒙原生应用开发交流群的群友提供的帮助。