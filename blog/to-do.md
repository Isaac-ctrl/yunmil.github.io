```
//
//  ToDoItem.swift
//  Todo
//
//  Copyright © 2016 YiGu. All rights reserved.
//

import Foundation

class ToDoItem: NSObject {
  var id: String
  var image: String
  var title: String
  var date: Date
  
  init(id: String, image: String, title: String, date: Date) {
    self.id = id
    self.image = image
    self.title = title
    self.date = date
  }
}
//
//  Utils.swift
//  Todo
//
//  Copyright © 2016 YiGu. All rights reserved.
//

import Foundation

func dateFromString(_ date: String) -> Date? {
  let dateFormatter = DateFormatter()
  dateFormatter.dateFormat = "MM-dd-yyyy"
  return dateFormatter.date(from: date)
}

func stringFromDate(_ date: Date) -> String {
  let dateFormatter = DateFormatter()
  dateFormatter.dateFormat = "MM-dd-yyyy"
  return dateFormatter.string(from: date)
}

//
//  ViewController.swift
//  Todo
//
//  Copyright © 2016 YiGu. All rights reserved.
//  code-projects.org/to-do-list-application-in-swift-with-source-code/

import UIKit

var todos: [ToDoItem] = []

class ViewController: UIViewController {
  
  @IBOutlet weak var todoTableView: UITableView!
  
  override func viewDidLoad() {
    super.viewDidLoad()
    
    navigationItem.leftBarButtonItem = editButtonItem
    
    todos = [ToDoItem(id: "1", image: "child-selected", title: "Go to Disney", date: dateFromString("10-20-2021")!),
             ToDoItem(id: "2", image: "shopping-cart-selected", title: "Costco Shopping", date: dateFromString("10-28-2021")!),
             ToDoItem(id: "3", image: "phone-selected", title: "Apply Jobs", date: dateFromString("10-20-2021")!),
             ToDoItem(id: "4", image: "travel-selected", title: "NYC", date: dateFromString("10-21-2021")!)]
  }
  
  override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    todoTableView.reloadData()
  }
  
  // MARK - helper func
  func setMessageLabel(_ messageLabel: UILabel, frame: CGRect, text: String, textColor: UIColor, numberOfLines: Int, textAlignment: NSTextAlignment, font: UIFont) {
    messageLabel.frame = frame
    messageLabel.text = text
    messageLabel.textColor = textColor
    messageLabel.numberOfLines = numberOfLines
    messageLabel.textAlignment = textAlignment
    messageLabel.font = font
    messageLabel.sizeToFit()
  }
  
  func setCellWithTodoItem(_ cell: UITableViewCell, todo: ToDoItem) {
    let imageView: UIImageView = cell.viewWithTag(11) as! UIImageView
    let titleLabel: UILabel = cell.viewWithTag(12) as! UILabel
    let dateLabel: UILabel = cell.viewWithTag(13) as! UILabel
    
    imageView.image = UIImage(named: todo.image)
    titleLabel.text = todo.title
    dateLabel.text = stringFromDate(todo.date)
  }
  
  override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if segue.identifier == "editTodo" {
      let vc = segue.destination as! DetailViewController
      let indexPath = todoTableView.indexPathForSelectedRow
      if let indexPath = indexPath {
        vc.todo = todos[(indexPath as NSIndexPath).row]
      }
    }
  }
}

extension ViewController: UITableViewDataSource {
  func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    
    if todos.count != 0 {
      return todos.count
    } else {
      let messageLabel: UILabel = UILabel()
      
      setMessageLabel(messageLabel, frame: CGRect(x: 0, y: 0, width: self.view.bounds.size.width, height: self.view.bounds.size.height), text: "No data is currently available.", textColor: UIColor.black, numberOfLines: 0, textAlignment: NSTextAlignment.center, font: UIFont(name:"Palatino-Italic", size: 20)!)
      
      self.todoTableView.backgroundView = messageLabel
      self.todoTableView.separatorStyle = UITableViewCellSeparatorStyle.none
      
      return 0
    }
  }
  
  func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cellIdentifier: String = "todoCell"
    let cell = tableView.dequeueReusableCell(withIdentifier: cellIdentifier, for: indexPath)
    
    setCellWithTodoItem(cell, todo: todos[(indexPath as NSIndexPath).row])
    
    return cell
  }
}

extension ViewController: UITableViewDelegate {
  // Edit mode
  override func setEditing(_ editing: Bool, animated: Bool) {
    super.setEditing(editing, animated: animated)
    todoTableView.setEditing(editing, animated: true)
  }
  
  // Delete the cell
  func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
    if editingStyle == UITableViewCellEditingStyle.delete {
      todos.remove(at: (indexPath as NSIndexPath).row)
      todoTableView.deleteRows(at: [indexPath], with: UITableViewRowAnimation.automatic)
    }
  }
  
  // Move the cell
  func tableView(_ tableView: UITableView, canMoveRowAt indexPath: IndexPath) -> Bool {
    return self.isEditing
  }
  
  func tableView(_ tableView: UITableView, moveRowAt sourceIndexPath: IndexPath, to destinationIndexPath: IndexPath) {
    let todo = todos.remove(at: (sourceIndexPath as NSIndexPath).row)
    todos.insert(todo, at: (destinationIndexPath as NSIndexPath).row)
  }
}


//
//  DetailViewController.swift
//  Todo
//
//  Copyright © 2016 YiGu. All rights reserved.
//

import UIKit

class DetailViewController: UIViewController {
  
  @IBOutlet weak var childButton: UIButton!
  @IBOutlet weak var phoneButton: UIButton!
  @IBOutlet weak var shoppingCartButton: UIButton!
  @IBOutlet weak var travelButton: UIButton!
  @IBOutlet weak var todoTitleLabel: UITextField!
  @IBOutlet weak var todoDatePicker: UIDatePicker!
  
  var todo: ToDoItem?
  
  override func viewDidLoad() {
    super.viewDidLoad()
    
    if let todo = todo {
      self.title = "Edit Todo"
      if todo.image == "child-selected"{
        childButton.isSelected = true
      }
      else if todo.image == "phone-selected"{
        phoneButton.isSelected = true
      }
      else if todo.image == "shopping-cart-selected"{
        shoppingCartButton.isSelected = true
      }
      else if todo.image == "travel-selected"{
        travelButton.isSelected = true
      }
      
      todoTitleLabel.text = todo.title
      todoDatePicker.setDate(todo.date, animated: false)
    } else {
      title = "New Todo"
      childButton.isSelected = true
    }
  }
  
  // MARK: type select
  @IBAction func selectChild(_ sender: AnyObject) {
    resetButtons()
    childButton.isSelected = true
  }
  
  @IBAction func selectPhone(_ sender: AnyObject) {
    resetButtons()
    phoneButton.isSelected = true
  }
  
  @IBAction func selectShoppingCart(_ sender: AnyObject) {
    resetButtons()
    shoppingCartButton.isSelected = true
  }
  
  @IBAction func selectTravel(_ sender: AnyObject) {
    resetButtons()
    travelButton.isSelected = true
  }
  
  func resetButtons() {
    childButton.isSelected = false
    phoneButton.isSelected = false
    shoppingCartButton.isSelected = false
    travelButton.isSelected = false
  }
  
  // MARK: create or edit a new todo
  @IBAction func tapDone(_ sender: AnyObject) {
    var image = ""
    if childButton.isSelected {
      image = "child-selected"
    }
    else if phoneButton.isSelected {
      image = "phone-selected"
    }
    else if shoppingCartButton.isSelected {
      image = "shopping-cart-selected"
    }
    else if travelButton.isSelected {
      image = "travel-selected"
    }
    
    if let todo = todo {
      todo.image = image
      todo.title = todoTitleLabel.text!
      todo.date = todoDatePicker.date
    } else {
      let uuid = UUID().uuidString
      todo = ToDoItem(id: uuid, image: image, title: todoTitleLabel.text!, date: todoDatePicker.date)
      todos.append(todo!)
    }
    
    let _ = navigationController?.popToRootViewController(animated: true)
  }
  
  override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    super.touchesBegan(touches, with: event)
    view.endEditing(true)
  }
}






```
