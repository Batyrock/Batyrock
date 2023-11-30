from flask_login import UserMixin, LoginManager, login_user, login_required, current_user, logout_user
from flask_uploads import UploadSet, configure_uploads, IMAGES
from flask_mail import Mail, Message
from flask_script import Manager

app.config['SECRET_KEY'] = 'your-secret-key'
app.config['UPLOADED_PHOTOS_DEST'] = 'uploads'
photos = UploadSet('photos', IMAGES)
configure_uploads(app, photos)

login_manager = LoginManager(app)
login_manager.login_view = 'login'

mail = Mail(app)
manager = Manager(app)

class User(db.Model, UserMixin):

class Project(db.Model):
    image = db.Column(db.String(20), nullable=False, default='default.jpg')

class ContributionForm(FlaskForm):
    image = FileField('Add Image', validators=[FileAllowed(IMAGES, 'Images only!')])
@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

@app.route("/login", methods=['GET', 'POST'])
def login():
    if current_user.is_authenticated:
        return redirect(url_for('home'))
    form = LoginForm()
    if form.validate_on_submit():
        user = User.query.filter_by(email=form.email.data).first()
        if user and bcrypt.check_password_hash(user.password, form.password.data):
            login_user(user, remember=form.remember.data)
            return redirect(url_for('home'))
        else:
            flash('Login unsuccessful. Please check email and password', 'danger')
    return render_template('login.html', form=form)

@app.route("/project/<int:project_id>/contribute", methods=['GET', 'POST'])
@login_required
def contribute(project_id):
    form = ContributionForm()
    project = Project.query.get_or_404(project_id)
    if form.validate_on_submit():
        if form.image.data:
            filename = photos.save(form.image.data)
            project.image = filename
        contribution = Contribution(content=form.content.data, contributor=current_user, project=project)
        db.session.add(contribution)
        db.session.commit()
        flash('Your contribution has been posted!', 'success')
        return redirect(url_for('project', project_id=project.id))
    return render_template('contribute.html', title='Contribute', form=form, project=project)

def send_reset_email(user):
    token = user.get_reset_token()
    msg = Message('Password Reset Request',
                  sender='noreply@example.com',
                  recipients=[user.email])
    msg.body = f'''To reset your password, visit the following link:
{url_for('reset_token', token=token, _external=True)}

If you did not make this request then simply ignore this email and no changes will be made.
'''
    mail.send(msg)

@manager.command
def create_db():
    db.create_all()

if _name_ == '_main_':
    manager.run()
