#### 修改root密码
```
gitlab-rails console -e production

user = User.where(id: 1).first或者user = User.find_by(email: ‘admin@local.host‘)

user.password = 'secret_pass'
user.password_confirmation = 'secret_pass'

user.save!
```
