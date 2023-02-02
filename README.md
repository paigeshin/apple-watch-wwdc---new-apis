# apple-watch-wwdc---new-apis

https://developer.apple.com/videos/play/wwdc2022/10133/

### Principles of Components

Heirarchical => Views with list-detail relationship => NavigationStack

Page-based => Flat collection => TabView

Full screen => Any full-screen content => ignoreSafeArea and toolbar modifiers

Modal sheet => Important tasks => sheet modifier 

```swift
//
//  ContentView.swift
//  ProductiveWatch Watch App
//
//  Created by paige shin on 2023/02/03.
//

import SwiftUI
import Charts

// Binding With Model
struct ContentView: View {
    
    @EnvironmentObject var model: ItemListModel
    
    var body: some View {
    
        List($model.items) { $item in
            Text(item.description)
        }
        .toolbar {
            AddItemLink()
        }
        .navigationTitle("Tasks")
    }
    
    
}

/// Stepper
struct StressStepper: View {
    
    private let stressLevels = [
        "😂","😙","🤨","😖","☹️"
    ]
    
    @State private var stressLevelIndex = 5
    
    var body: some View {
        
        VStack {
            Text("Stress Level")
                .font(.system(.footnote, weight: .bold))
                .foregroundStyle(.tint)
            
            Stepper(value: $stressLevelIndex, in: (0...stressLevels.count-1)) {
                Text(stressLevels[stressLevelIndex])
            }
            
        }
        
    }
    
    
}

/// ShareLink
struct ShareMe: View {
    
    var body: some View {
        
        ShareLink(
            item: "something",
            subject: .init("help"),
            message: .init("heelo"),
            preview: SharePreview("hello")
        )
        .buttonStyle(.borderedProminent)
        .buttonBorderShape(.roundedRectangle)
        
    }
    
}

/// Tabview
struct HomeView: View {
    
    var body: some View {
        TabView {
            
            NavigationStack {
                Text("ITEM LIST")
            }
            NavigationStack {
                ProductivityChart()
            }
            
        }
        .tabViewStyle(.page)
    }
    
}

/// Chart
struct ChartData {
    
    struct DataElement: Identifiable {
        let id: String = UUID().uuidString
        let date: Date
        let itemsComplete: Double
    }
    
    static func create(items: [ListItem]) -> [DataElement] {
        return Dictionary(grouping: items, by: \.completionDate)
            .map {
                return DataElement(date: $0, itemsComplete: Double($1.count))
            }
            .sorted {
                $0.date < $1.date
            }
    }
    
}


struct ProductivityChart: View {
    
    let data = ChartData.create(items: [
        ListItem(description: "abc", completionDate: Date()),
        ListItem(description: "abcdefg", completionDate: Date().addingTimeInterval(60 * 60 * 24))
    ])
    
    var body: some View {
        
        Chart(data) { dataPoint in
            BarMark(
                x: .value("Date", dataPoint.date),
                y: .value("Completed", dataPoint.itemsComplete)
            )
            .foregroundStyle(Color.accentColor)
        }
        .chartXAxis {
            AxisMarks { value in
                AxisGridLine(stroke: StrokeStyle(lineWidth: 0.5))
                    .foregroundStyle(Color.gray)
                if value.index < value.count - 1 {
                    AxisValueLabel(format: .dateTime)
                }
            }
      
        }
        .navigationTitle("Productivity")
        
    }
    
}

/// TextFieldLink
struct AddItemLink: View {
    
    @EnvironmentObject var model: ItemListModel
    
    var body: some View {
        VStack {
            TextFieldLink(prompt: Text("New Item")) {
                Label(
                    "Add",
                    systemImage: "plus.circle.fill"
                )
            } onSubmit: {
                model.items.append(ListItem(description: $0))
            }
        }
    }
    
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}



```
