---
title: Encode和Decode
date: 2023-07-31 14:44:27
tags: [iOS, Swift]
categories:
- [Swift]
---
## 概述
许多编程任务需要用到数据的编码（encode）和解码（decode），把数据转变为一种中间格式，例如通过网络传输数据、把数据存进磁盘、把数据提交给API和服务等。

Swift标准库定义了一种标准化的数据编码和解码方法。我们可以通过在我们的自定义类型上实现`Encodable`和`Decodable`协议来采用该标准方法。

## Codable
是`Encodable`和`Decodable`协议的类型别名
```swift
typealias Codable = Decodable & Encodable
```
实现了`Codable`的类型可以自动被encode和decode
```swift
struct Landmark: Codable {
    var name: String
    var foundingYear: Int
    var location: Coordinate

    // Landmark is still codable after adding these properties.
    var vantagePoints: [Coordinate]
    var metadata: [String: String]
    var website: URL?
}
```
## Coding keys
使用Coding keys主要是为了：
1. 选择哪些属性被Encode和Decode
2. 改变Encode和Decode的属性名
```swift
struct Landmark: Codable {
    var name: String
    var foundingYear: Int
    var location: Coordinate
    var vantagePoints: [Coordinate]

    enum CodingKeys: String, CodingKey {
        case name = "title"
        case foudingYear = "founding_date"

        case location
        case vantagePoints
    }
}
```

## 手动Encode和Decode
如果Swift类型和编码的格式不匹配，可以提供一个`Encodable`和`Decodable`的自定义实现

下面的例子中，`elevation`嵌套在`additionalInfo`中，于是自定义了`init(from:)`和`encode(to:)`方法，来达到多层级encode和decode的目的
```swift
struct Coordinate {
    var latitude: Double
    var longitude: Double
    var elevation: Double

    enum CodingKeys: String, CodingKey {
        case latitude
        case longitude
        case additionalInfo
    }

    enum AdditionalInfoKeys: String, CodingKey {
        case elevation
    }
}

extension Coordinate: Decodable {
    init(from decoder: Decoder) throws {
        let values = try decoder.container(keyedBy: CodingKeys.self)
        latitude = try values.decode(Double.self, forKey: .latitude)
        longitude = try values.decode(Double.self, forKey: .longitude)

        let additionalInfo = try values.nestedContainer(keyedBy: AdditionalInfoKeys.self, forKey: .additionalInfo)
        elevation = try additionalInfo.decode(Double.self, forKey: .elevation)
    }
}

extension Coordinate: Encodable {
    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encode(latitude, forKey: .latitude)
        try container.encode(longitude, forKey: .longitude)

        var additionalInfo = container.nestedContainer(keyedBy: AddtionalInfoKeys.self, forKey: .additionalInfo)
        try additionalInfo.encode(elevation, forKey: .elevation)
    }
}
```
