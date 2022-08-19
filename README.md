# Combinestagram

## RxSwift: Reactive Programming with Swift | raywenderlich.com
![image](https://user-images.githubusercontent.com/47273077/185172130-b3557025-c636-4a1b-8490-c900c8312b77.png)

### 1. Using a subject/relay in a view controller
<img width="516" src="https://user-images.githubusercontent.com/47273077/185170770-507692fc-adc2-4837-8ba6-5ba48032c0fc.gif">

MainViewController
```swift
class MainViewController: UIViewController {

  @IBOutlet weak var imagePreview: UIImageView!
  @IBOutlet weak var buttonClear: UIButton!
  @IBOutlet weak var buttonSave: UIButton!
  @IBOutlet weak var itemAdd: UIBarButtonItem!
  
  private let bag = DisposeBag()
  private let images = BehaviorRelay<[UIImage]>(value: [])

  override func viewDidLoad() {
    super.viewDidLoad()
    
    images.subscribe(onNext: { [weak imagePreview] photos in
      guard let preview = imagePreview else { return }
      
      preview.image = photos.collage(size: preview.frame.size)
    })
    .disposed(by: bag)

  }
  
  @IBAction func actionClear() {
    images.accept([])
  }

  @IBAction func actionSave() {

  }

  @IBAction func actionAdd() {
    let newImages = images.value + [UIImage(named: "IMG_1907.jpg")!]
    images.accept(newImages)

  }

  func showMessage(_ title: String, description: String? = nil) {
    let alert = UIAlertController(title: title, message: description, preferredStyle: .alert)
    alert.addAction(UIAlertAction(title: "Close", style: .default, handler: { [weak self] _ in self?.dismiss(animated: true, completion: nil)}))
    present(alert, animated: true, completion: nil)
  }
}
```


### 2. Driving a complex view controller UI

<img width="516" src="https://user-images.githubusercontent.com/47273077/185615339-9b49247e-ca90-4aa0-8725-b4373f3ed227.gif">

```swift

  override func viewDidLoad() {
    super.viewDidLoad()
    
    // ------- 中略 ------- 
    
    images.subscribe(onNext: { [weak self] photos in
      self?.updateUI(photos: photos)
    })
    .disposed(by: bag)

  }

  private func updateUI(photos: [UIImage]) {
    buttonSave.isEnabled = photos.count > 0 && photos.count % 2 == 0
    buttonClear.isEnabled = photos.count > 0
    itemAdd.isEnabled = photos.count < 6
    title = photos.count > 0 ? "\(photos.count) photos" : "Collage"
    
  }
```
