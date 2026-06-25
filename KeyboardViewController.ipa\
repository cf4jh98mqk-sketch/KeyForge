import UIKit

class KeyboardViewController: UIInputViewController {

    var customFont: UIFont?
    var keyColor: UIColor = UIColor(red: 0.16, green: 0.16, blue: 0.21, alpha: 1)
    var keyTextColor: UIColor = UIColor(red: 0, green: 1, blue: 1, alpha: 1)
    var boardBgColor: UIColor = UIColor(red: 0.08, green: 0.08, blue: 0.1, alpha: 1)
    var fontSize: CGFloat = 18
    var isShifted: Bool = false
    var isCapsLock: Bool = false

    let keyboardRows: [[String]] = [
        ["1","2","3","4","5","6","7","8","9","0"],
        ["q","w","e","r","t","y","u","i","o","p"],
        ["a","s","d","f","g","h","j","k","l"],
        ["⇧","z","x","c","v","b","n","m","⌫"],
        ["123","space","return"]
    ]

    var keyButtons: [UIButton] = []
    var mainStack: UIStackView!

    override func viewDidLoad() {
        super.viewDidLoad()
        loadCustomFont()
        setupKeyboard()
    }

    func loadCustomFont() {
        let fontName = "CustomKeyboardFont"
        if let font = UIFont(name: fontName, size: fontSize) {
            customFont = font
        } else {
            customFont = UIFont.systemFont(ofSize: fontSize)
        }
    }

    func setupKeyboard() {
        view.backgroundColor = boardBgColor
        view.subviews.forEach { $0.removeFromSuperview() }
        keyButtons.removeAll()

        mainStack = UIStackView()
        mainStack.axis = .vertical
        mainStack.spacing = 8
        mainStack.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(mainStack)

        NSLayoutConstraint.activate([
            mainStack.topAnchor.constraint(equalTo: view.topAnchor, constant: 8),
            mainStack.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 4),
            mainStack.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -4),
            mainStack.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -8)
        ])

        for row in keyboardRows {
            let rowStack = UIStackView()
            rowStack.axis = .horizontal
            rowStack.spacing = 6
            rowStack.distribution = .fillProportionally

            for keyLabel in row {
                let button = makeKey(label: keyLabel)
                rowStack.addArrangedSubview(button)
                keyButtons.append(button)

                if keyLabel == "space" {
                    button.widthAnchor.constraint(equalToConstant: 200).isActive = true
                } else if keyLabel == "return" || keyLabel == "123" {
                    button.widthAnchor.constraint(equalToConstant: 88).isActive = true
                } else if keyLabel == "⇧" || keyLabel == "⌫" {
                    button.widthAnchor.constraint(equalToConstant: 60).isActive = true
                }
            }

            mainStack.addArrangedSubview(rowStack)
        }
    }

    func makeKey(label: String) -> UIButton {
        let button = UIButton(type: .system)
        button.translatesAutoresizingMaskIntoConstraints = false
        button.heightAnchor.constraint(equalToConstant: 46).isActive = true

        let isSpecial = ["⇧", "⌫", "return", "space", "123"].contains(label)
        let displayLabel = label == "space" ? "space" : label == "return" ? "return" : label

        button.setTitle(displayLabel, for: .normal)
        button.setTitleColor(keyTextColor, for: .normal)
        button.backgroundColor = isSpecial
            ? keyColor.withAlphaComponent(0.6)
            : keyColor
        button.layer.cornerRadius = 8
        button.layer.shadowColor = UIColor.black.cgColor
        button.layer.shadowOffset = CGSize(width: 0, height: 2)
        button.layer.shadowOpacity = 0.4
        button.layer.shadowRadius = 3

        if let font = customFont, !isSpecial {
            button.titleLabel?.font = font.withSize(fontSize)
        } else {
            button.titleLabel?.font = UIFont.systemFont(ofSize: label == "space" || label == "return" || label == "123" ? 13 : 16, weight: .medium)
        }

        button.addTarget(self, action: #selector(keyTapped(_:)), for: .touchUpInside)
        button.addTarget(self, action: #selector(keyDown(_:)), for: .touchDown)
        button.addTarget(self, action: #selector(keyUp(_:)), for: [.touchUpInside, .touchUpOutside, .touchCancel])
        button.accessibilityLabel = label

        return button
    }

    @objc func keyDown(_ sender: UIButton) {
        UIView.animate(withDuration: 0.05) {
            sender.transform = CGAffineTransform(scaleX: 0.92, y: 0.92)
            sender.alpha = 0.8
        }
    }

    @objc func keyUp(_ sender: UIButton) {
        UIView.animate(withDuration: 0.1) {
            sender.transform = .identity
            sender.alpha = 1
        }
    }

    @objc func keyTapped(_ sender: UIButton) {
        guard let label = sender.accessibilityLabel else { return }

        switch label {
        case "⌫":
            textDocumentProxy.deleteBackward()
        case "return":
            textDocumentProxy.insertText("\n")
        case "space":
            textDocumentProxy.insertText(" ")
        case "⇧":
            isShifted = !isShifted
            updateShiftState()
        case "123":
            advanceToNextInputMode()
        default:
            let char = isShifted ? label.uppercased() : label
            textDocumentProxy.insertText(char)
            if isShifted && !isCapsLock {
                isShifted = false
                updateShiftState()
            }
        }
    }

    func updateShiftState() {
        for button in keyButtons {
            guard let label = button.accessibilityLabel else { continue }
            let letters = "abcdefghijklmnopqrstuvwxyz"
            if letters.contains(label) {
                let displayed = isShifted ? label.uppercased() : label
                button.setTitle(displayed, for: .normal)
            }
        }
    }
}
