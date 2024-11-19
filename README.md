# **شيموا (chezmoi) ببساطة**
يمكنك مشاهدة فيديو لشرح طريقة الاستخدام على يوتيوب من هنا:  
[شاهد الفيديو على يوتيوب](https://www.youtube.com/watch?v=Jrrd2dHYBmY)

## **ما هي ملفات dotfile؟**

ملفات dotfile هي ملفات تكوين/اعدادت تتحكم في كيفية تصرف البرامج والأدوات المختلفة على نظامك. عادةً ما تبدأ هذه الملفات بنقطة (`.`)، مثل `.gitconfig` أو `.bashrc`، وتكون مخفية بشكل افتراضي.

تحتوي هذه الملفات على إعدادات مخصصة لأدوات مثل الصدفة (shell)، المحرر، والطرفية. إذا كنت تستخدم عدة أجهزة، فإن مزامنة هذه الملفات يضمن أن بيئتك تظل متسقة عبر جميع الأجهزة.

> **نصيحة:** أدوات مثل **chezmoi** تساعدك في إدارة ومزامنة وتثبيت ملفات dotfile. يمكنك استكشاف المزيد من الأدوات لإدارة ملفات النقاط على [إدارة dotfiles](https://dotfiles.github.io/utilities/).

## [**سير العمل الأساسي**](https://www.chezmoi.io/quick-start/)

لبدء استخدام `chezmoi`, اتبع هذه الخطوات الأساسية:

---

### **1. تهيئة chezmoi**  
قم بإعداد **chezmoi** لإدارة ملفات dotfiles من خلال تهيئة مستودع محلي:

```bash  
chezmoi init  
```  

- على **Windows**، سيقوم هذا بإنشاء مجلد في `%USERPROFILE%\AppData\Local\chezmoi` لتخزين ملفات النقاط.
- على **Linux**، عادةً ما يكون الدليل في `~/.local/share/chezmoi`.

---

### **2. الانتقال مستودع chezmoi المحلي**  
لإدارة ملفات dotfiles الخاصة بك مباشرة أو التفاعل مع Git، ادخل إلى المستودع المحلي chezmoi:

```bash
chezmoi cd
```

سيؤدي هذا إلى فتح المجلد الذي يخزن فيه chezmoi ملفات dotfiles. من هنا، يمكنك:
- عرض أو تحرير ملفات dotfiles.
- استخدام Git للتحكم في إصدار التغييرات (مثل `git add`، `git commit`).

---

### **3. إضافة dotfile**  
لبدء إدارة ملف باستخدام chezmoi، استخدم أمر `add`:

```bash  
chezmoi add ~/.gitconfig  
```  

- على **Linux**، يقوم هذا الأمر بنسخ `~/.gitconfig` إلى مستودع chezmoi المحلي كـ `dot_gitconfig`.
- على **Windows**، يمكنك تحديد المسار الكامل أو استخدام الاختصار `~`:
  ```bash  
  chezmoi add C:\Users\<YourUsername>\.gitconfig  
  ```  
  أو  
  ```bash  
  chezmoi add ~/.gitconfig  
  ```  
  سيتم تخزين الملف كـ `dot_gitconfig` في مستودع chezmoi.

---

### **4. معاينة التغييرات**  
قبل تطبيق التغييرات على النظام الخاص بك، قم بمعاينة التغييرات باستخدام:

```bash  
chezmoi diff  
```

---

### **5. تطبيق التغييرات**  
لتحديث نظامك بملفات dotfiles التى يتم إدراتها من قبل chezmoi، استخدم:

```bash  
chezmoi -v apply  
```  

توفر علامة `-v` (التفصيلية) سجلات مفصلة للتغييرات التي سوف يتم إجراؤها. ويمكنك استخدام ببساطة بدون عرض التغيرات:

```bash  
chezmoi apply  
```

---

### **6. إدارة dotfiles فى المستودع المحلي**  
بمجرد الدخول إلى المستودع chezmoi (`chezmoi cd`)، يمكنك استخدام Git لإدارة ملفات dotfiles الخاصة بك:

```bash
git add .
git commit -m "تتبع تغييرات .gitconfig"
```

## [**القوالب**](https://www.chezmoi.io/user-guide/templating/#template-data)

تسمح لك القوالب في chezmoi بتعديل محتويات الملفات ديناميكيًا بناءً على البيئة. على سبيل المثال، قد ترغب في إعدادات تكوين مختلفة على أجهزة مختلفة حسب  hostname او نظام التشغيل os.

### **بيانات القوالب**

يوفر chezmoi العديد من المتغيرات الجهازة مسبقا التي يمكنك استخدامها في إدارة ملفات dotfiles حسب الحاجة. لرؤية قائمة بالمتغيرات المعرفة مسبقًا، قم بتشغيل:

```bash
chezmoi data
```

تأتي هذه المتغيرات من مصادر مختلفة، وترتيب الأولوية لها كالتالي:
1. **المتغيرات في `.chezmoi`**، مثل `.chezmoi.os`
2. **المتغيرات المعرفة في قسم `[data]` من ملف التكوين `chezmoi.toml`**

### **تعديل ملف  `chezmoi.toml`**

لإنشاء أو تعديل ملف `chezmoi.toml`، استخدم أمر `chezmoi edit-config`:

```bash
chezmoi edit-config
```

سيؤدي هذا إلى فتح ملف `chezmoi.toml` في المحرر الافتراضي لديك، حيث يمكنك إضافة بيانات مخصصة.

### **استخدام قسم `[data]` في `chezmoi.toml`**

في قسم `[data]` من `chezmoi.toml`، يمكنك تعريف متغيرات مخصصة لاستخدامها في قوالبك. مثلا:

```toml
[data]
email = "foo@foo.com"
name = "foo"
others = "bla bla"
```

يمكن الآن الإشارة إلى هذه القيم داخل قوالبك.

### **إنشاء ملف كقالب**

عند إضافة ملف جديد إلى chezmoi، يمكنك تحديده كقالب باستخدام الخيار `--template`:

```bash
chezmoi add --template ~/.gitconfig
```

إذا كان الملف قد تمت إدارته بواسطة chezmoi بالفعل ولكنك ترغب في تحويله إلى قالب، استخدم أمر `chattr`:

```bash
chezmoi chattr +template ~/.gitconfig
```

سيتم التعامل مع هذا الملف كقالب، مما يسمح لك باستخدام المتغيرات المعرفة في ملف `chezmoi.toml` أو مصادر بيانات القوالب الأخرى.

---

## [**التشفير**](https://www.chezmoi.io/user-guide/frequently-asked-questions/encryption/)

سنقوم بتكوين **chezmoi** لتشفير الملفات الحساسة مع ضمان أنك تحتاج فقط إلى تقديم كلمة مرور في المرة الأولى التي تقوم فيها بتشغيل `chezmoi init`. سنستخدم **age**، وهي أداة تشفير بسيطة وآمنة.

#### 1. **إنشاء مفتاح خاص لـ Age**
أولاً، أنشئ مفتاح خاص جديد لـ **age** مشفر باستخدام كلمة مرور. قم بتشغيل الأمر التالي في المستودع المحلي الخاص بك:

```bash
age-keygen | age --armor --passphrase > key.txt.age
```

- ستتم مطالبتك بإدخال **كلمة مرور** (تأكد من أنها قوية). يتم استخدام هذه الكلمة لتشفير المفتاح الخاص بك.
- بعد هذه العملية، لاحظ **المفتاح العام** (على سبيل المثال: `age193wd0hfuhtjfsunlq3c83s8m93pde442dkcn7lmj3lspeekm9g7stwutrl`)، الذي سيتم استخدامه في التكوين.

#### 2. **تجاهل مفتاح التشفر**  
لمنع chezmoi من إدارة المفتاح المشفر، أضف `key.txt.age` إلى ملف `.chezmoiignore`:

```bash
echo key.txt.age >> .chezmoiignore
```

#### 3. **فك تشفير المفتاح الخاص عند الحاجة**  
أنشئ ملف سكريبت سيفك تشفير مفتاحك الخاص عند تهيئة chezmoi. سيعمل هذا السكربت مرة واحدة عند الحاجة.

**لـ Linux/Unix**:  
استخدم هذا السكربت shell (`run_once_before_decrypt-private-key.sh.tmpl`):

```bash
cat > run_once_before_decrypt-private-key.sh.tmpl <<EOF
#!/bin/sh

if [ ! -f "${HOME}/.config/chezmoi/key.txt" ]; then
    mkdir -p "${HOME}/.config/chezmoi"
    chezmoi age decrypt --output "${HOME}/.config/chezmoi/key.txt" --passphrase "{{ .chezmoi.sourceDir }}/key.txt.age"
    chmod 600 "${HOME}/.config/chezmoi/key.txt"
fi
EOF
```

سيضمن هذا السكربت فك تشفير المفتاح الخاص فقط عند الحاجة وتخزينه بشكل آمن في `~/.config/chezmoi/key.txt`.

**لـ Windows (PowerShell)**:  
على Windows، يختلف العملية. استخدم هذا السكربت PowerShell (`run_once_before_decrypt-private-key.ps1.tmpl`):

```powershell
# Define paths for the home directory and key files
$homePath = [Environment]::GetFolderPath("UserProfile")
$keyFilePath = Join-Path -Path $homePath -ChildPath ".config\chezmoi\key.txt"
$encryptedKeyPath = Join-Path -Path (chezmoi source-path) -ChildPath "key.txt.age"

# Check if the key file exists
if (!(Test-Path -Path $keyFilePath)) {
    # Create the .config/chezmoi directory if it doesn't exist
    $configPath = Join-Path -Path $homePath -ChildPath ".config\chezmoi"
    if (!(Test-Path -Path $configPath)) {
        New-Item -ItemType Directory -Path $configPath -Force | Out-Null
    }

    # Run

 chezmoi to decrypt the age-encrypted file
    chezmoi age decrypt --output $keyFilePath --passphrase $encryptedKeyPath
    
    # Set permissions: Read and Write access only for the current user
    $acl = New-Object System.Security.AccessControl.FileSecurity
    $accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule(
        $env:UserName, "Read, Write", "Allow"
    )
    $acl.AddAccessRule($accessRule)
    Set-Acl -Path $keyFilePath -AclObject $acl
}
```

### **4. إعداد chezmoi لاستخدام التشفير**
حدث ملف `chezmoi.toml` لتحديد إعدادات التشفير. يجب أن يبدو التكوين الخاص بك هكذا:

```toml
encryption = "age"
[age]
    identity = "~/.config/chezmoi/key.txt"
    recipient = "age193wd0hfuhtjfsunlq3c83s8m93pde442dkcn7lmj3lspeekm9g7stwutrl"
```

---

**ملاحظة:** تأكد من إضافة `encryption` إلى القسم العلوي في بداية التكوين، قبل أي أقسام أخرى.

Apologies for missing that part! Here’s the translation for the missing section you referred to:

---

#### **5. تهيئة chezmoi**  
بعد تكوين التشفير، قم بتشغيل `chezmoi init` لتهيئة النظام وفك تشفير المفتاح الخاص:

```bash
chezmoi init --apply
```

سيتم طلب منك إدخال كلمة المرور الخاصة بك لفك تشفير `key.txt.age`. ثم سيتم تخزين المفتاح الخاص في `~/.config/chezmoi/key.txt`.

#### **6. التحقق من الحالة**  
قم بتشغيل `git status` للتحقق من حالة الملفات:

```bash
git status
```

سترى ملفات غير متعقبة مثل:

```
Untracked files:
  (use "git add <file>..." to include in what will be committed)
    .chezmoi.toml.tmpl
    .chezmoiignore
    key.txt.age
    run_once_before_decrypt-private-key.sh.tmpl
```

#### **7. إضافة الملفات المشفرة**  
لإضافة ملفات للتشفير باستخدام chezmoi، استخدم الخيار `--encrypt`. على سبيل المثال، لتشفير مفتاح SSH الخاص:

```bash
chezmoi add --encrypt ~/gitconfig
```
### ** فك التشفير على جهاز جديد**

عند تشغيل `chezmoi init` على جهاز جديد:
- ستحتاج فقط إلى إدخال كلمة المرور **مرة واحدة** لفك تشفير `key.txt.age`.
- سيتم تخزين المفتاح الخاص المفكوك في `~/.config/chezmoi/key.txt`.

بعد ذلك، سيتم إدارة الملفات المشفرة بواسطة chezmoi تلقائيًا، ولن تحتاج إلى إدخال كلمة المرور مرة أخرى إلا إذا تم حذف المفتاح أو إذا تم إعادة تهيئة الجهاز.