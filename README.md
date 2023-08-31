```swift
import UIKit

final class ProfileViewController: UIViewController {
    private let profileService = ProfileService.shared
    let profileView = ProfileView()
    
    override func loadView() {
        view = ProfileView(frame: .zero)
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        updateProfileDetails(profile: profileService.profile)
    }
    
    func updateProfileDetails(profile: Profile?) {
        guard let profile = profile else {
            return
        }
        nameLabel.text = profile.name
        loginLabel.text = profile.loginName
        descriptionLabel.text = profile.bio
        
        profileImageServiceObserver = NotificationCenter.default
            .addObserver(
                forName: ProfileImageService.didChangeNotification,
                object: nil,
                queue: .main
            ) { [weak self] _ in
                guard let self = self else { return }
                self.updateAvatar()
            }
        updateAvatar()
    }
    
    private func updateAvatar() {
        guard
            let profileImageURL = ProfileImageService.shared.avatarURL,
            let imageURL = URL(string: profileImageURL)
        else { return }
        let processor = RoundCornerImageProcessor(cornerRadius: 61)
        avatarImageView.kf.indicatorType = .activity
        avatarImageView.kf.setImage(with: imageURL,
                                    placeholder: UIImage(named: "Stub.png"),
                                    options: [.processor(processor)])
    }
}
```






```swift

import UIKit

final class ProfileView: UIView {
    
    private let profileImageView: UIImageView = {
        $0.translatesAutoresizingMaskIntoConstraints = false
        $0.contentMode = .scaleAspectFill
        return $0
    }(UIImageView(image: UIImage(named: "user")))
    
    private let profileName: UILabel = {
        $0.translatesAutoresizingMaskIntoConstraints = false
        $0.text = "Екатерина Новикова"
        $0.font = UIFont.boldSystemFont(ofSize: 23)
        $0.textColor = UIColor(red: 255/255, green: 255/255, blue: 255/255, alpha: 1)
        $0.numberOfLines = 1
        return $0
    }(UILabel(frame: .zero))
    
    private let profileLogin: UILabel = {
        $0.translatesAutoresizingMaskIntoConstraints = false
        $0.text = "@ekaterina_nov"
        $0.font = .preferredFont(forTextStyle: .footnote)
        $0.textColor = UIColor(red: 174/255, green: 175/255, blue: 180/255, alpha: 1)
        return $0
    }(UILabel(frame: .zero))
    
    private let profileDescription: UILabel = {
        $0.translatesAutoresizingMaskIntoConstraints = false
        $0.text = "Hello, world!"
        $0.font = .preferredFont(forTextStyle: .footnote)
        $0.textColor = UIColor(red: 255/255, green: 255/255, blue: 255/255, alpha: 1)
        return $0
    }(UILabel(frame: .zero))
    
    private let logOutButton: UIButton = {
        $0.translatesAutoresizingMaskIntoConstraints = false
        $0.setImage(UIImage(named: "ipad.and.arrow.forward"), for: .normal)
      return $0
    }(UIButton(frame: .zero))
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        backgroundColor = UIColor(red: 26/255, green: 27/255, blue: 34/255, alpha: 1)
        setupContraints()
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}

extension ProfileView {
    private func setupContraints() {
        addSubview(profileImageView)
        addSubview(profileName)
        addSubview(profileLogin)
        addSubview(profileDescription)
        addSubview(logOutButton)
        
        // Провести проверку по корректности лейблов (profileLogin, profileDescription) - trailing нужен ли для них или нет.
        NSLayoutConstraint.activate([
            profileImageView.topAnchor.constraint(equalTo: safeAreaLayoutGuide.topAnchor, constant: 32),
            profileImageView.leadingAnchor.constraint(equalTo: leadingAnchor, constant: 16),
            profileImageView.heightAnchor.constraint(equalToConstant: 70),
            profileImageView.widthAnchor.constraint(equalToConstant: 70),
            
            profileName.topAnchor.constraint(equalTo: profileImageView.bottomAnchor, constant: 8),
            profileName.leadingAnchor.constraint(equalTo: profileImageView.leadingAnchor),
             // Чтобы был отступ справа если будет к примеру такое имя: Константин Константинопольский
            profileName.trailingAnchor.constraint(equalTo: trailingAnchor, constant: -16),
            
            profileLogin.topAnchor.constraint(equalTo: profileName.bottomAnchor, constant: 8),
            profileLogin.leadingAnchor.constraint(equalTo: profileName.leadingAnchor),
            profileLogin.trailingAnchor.constraint(equalTo: profileName.trailingAnchor),
            
            profileDescription.topAnchor.constraint(equalTo: profileLogin.bottomAnchor, constant: 8),
            profileDescription.leadingAnchor.constraint(equalTo: profileName.leadingAnchor),
            profileDescription.trailingAnchor.constraint(equalTo: profileLogin.trailingAnchor),
            
            logOutButton.trailingAnchor.constraint(equalTo: trailingAnchor, constant: -20),
            logOutButton.centerYAnchor.constraint(equalTo: profileImageView.centerYAnchor)
        ])
    }
}

```
