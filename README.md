#include <iostream>
#include <string>
using namespace std;

// الحد الأقصى للحسابات
const int MAX_USERS = 100;

// مصفوفات لتخزين البيانات
string usernames[MAX_USERS];
string passwords[MAX_USERS];
double balances[MAX_USERS];
int loginAttempts[MAX_USERS];
bool accountLocked[MAX_USERS];

// عدد الحسابات الحالية
int userCount = 0;

// دالة للبحث عن اسم المستخدم وإرجاع الفهرس
int findUserIndex(const string &name)
{
    // تحقق من كل حساب حتى تجد الاسم المطلوب
    for (int i = 0; i < userCount; i++)
    {
        if (usernames[i] == name)
        {
            return i;
        } // إرجاع الفهرس إذا وجدت الاسم
    }

        return -1; // لم يتم العثور على المستخدم
}

// دالة للتحقق من صحة اسم المستخدم
bool isValidUsername(const string &name)
{
    // تحقق من كل حرف في الاسم
    for (char c : name)
    {
        if (!isalpha(c))// إذا كان الحرف ليس حرفًا أبجديًا
        {
            return false; // إرجاع خطأ
        }
    }

    return true; // الاسم صحيح
}

bool isNumericPassword(const string &password)
{
        for (char c : password) {
            if (!isdigit(c))// يجب أن يكون رقمًا فقط
            {
                return false;
            }
    }

        return true;
}

// دالة لعرض شعار البنك
void displayLogo()
{
    cout << "************************************\n";
    cout << "*         Bank The Winners         *\n";
    cout << "************************************\n";
}

// دالة لعرض القائمة الرئيسية
void showMenu()
{
  cout << "\n<<< WELCOME TO OUR BANK >>>\n\n";
  cout << "[1] Create New Account\n";
  cout << "[2] Log In\n";
  cout << "[3] Exit\n";
  cout << "\nEnter your choice: ";
}

bool isValidAccountName(const string &name)
{
    if (name.empty())// التحقق من أن الاسم غير فارغ
    {
        return false;
    }

    for (char c : name)
    {
        if (!isalpha(c))// التأكد من أن كل حرف هو حرف إنجليزي فقط
        {
            return false;
        }
    }

    return true; // الاسم صالح إذا كان كله حروف إنجليزية
}

// دالة لإنشاء حساب جديد
void createAccount()
{
    // تحقق من أن عدد الحسابات لم يصل إلى الحد الأقصى
    if (userCount >= MAX_USERS)
    {
        cout << "\nSorry, the bank has reached the maximum number of accounts.\n";
        return;
    }

    string name, password, confirmPassword;
    double initialBalance;

    // طلب اسم المستخدم وتحقق من صحته
    do
    {
        cout << "\nEnter Your Account Name (MinaMamdouh) Without space: ";
        cin.ignore();
        getline(cin, name);

        if (!isValidAccountName(name))
        {
            cout << "\nInvalid account name! Use English letters only without spaces\n";
            continue;
        }

        if (findUserIndex(name) != -1)
        {
            cout << "\nAccount name already taken, please choose another one.\n";
            continue;
        }

        break;
    } while (true);

  // طلب كلمة المرور وتحقق من تطابقها مع التأكيد
  do
    {
        cout << "\nEnter a 4-digit password: ";
        cin >> password;
        if (password.length() != 4 || !isNumericPassword(password))
        {
            cout << "\nPassword must be exactly 4 digits and contain numbers only.\n";
            continue;
        }
        if (password.length() !=4)
        {
            cout << "\nPassword must not exceed 4 digits.\n";
            continue;
        }

        cout << "\nConfirm your password: ";
        cin >> confirmPassword;

        if (password != confirmPassword)
        {
            cout << "\nPasswords do not match. Try again.\n";
        }
    } while (password != confirmPassword);

// طلب الرصيد الأولي
  while (true)
  {
    cout << "\nEnter initial deposit amount: ";
    cin >> initialBalance;

    // التحقق من الإدخال الصحيح
    if (cin.fail() || initialBalance < 0)
    {
        cout << "\nInvalid amount! Please enter a valid positive number.\n";
        cin.clear(); // تصحيح خطأ الإدخال
        cin.ignore(10000, '\n'); // حذف الإدخال الخاطئ
        continue;
    }
    break; // الخروج من الحلقة عند الإدخال الصحيح
 }

  // تخزين البيانات في المصفوفات
  usernames[userCount] = name;
  passwords[userCount] = password;
  balances[userCount] = initialBalance;
  loginAttempts[userCount] = 0;
  accountLocked[userCount] = false;

  // زيادة عدد الحسابات
  userCount++;

  cout << "\nAccount Created Successfully!\n";
}

// إعلان عن الدوال
void bankingServices(int userIndex);
void transferMoney(int userIndex);
void closeAccount(int userIndex);
void changePassword(int userIndex);

// دالة لتسجيل الدخول
void login()
{
    string name, password;
    cout << "\nEnter Your Account Name: ";
    cin.ignore();
    getline(cin, name);

    // تحديد فهرس المستخدم
    int userIndex = findUserIndex(name);

    if (userIndex == -1)
    {
        cout << "\nAccount not found!\n";
        return;
    }

    // تحقق من أن الحساب غير مقفل
    if (accountLocked[userIndex])
    {
        cout << "\nYour account is locked. Please contact customer service.\n";
        return;
    }

    // طلب كلمة المرور وتحقق من صحتها
    for (int attempts = 0; attempts < 3; attempts++)
    {
        cout << "\nEnter Your Password: ";
        cin >> password;

        if (passwords[userIndex] == password)
        {
            cout << "\nLogin Successful!\n";
            bankingServices(userIndex);
            return;
        }
        else
        {
            cout << "\nIncorrect password. Try again.\n";
        }
    }

    // قفل الحساب بعد ثلاث محاولات فاشلة
    cout << "\nAccount locked. Please contact customer service.\n";
    accountLocked[userIndex] = true;
}

// دالة لقائمة الخدمات البنكية
void bankingServices(int userIndex)
{
    int choice;
    do
    {
        // عرض الخيارات
        cout << "\n[1] Deposit Money\n";
        cout << "[2] Withdraw Money\n";
        cout << "[3] Check Balance\n";
        cout << "[4] Transfer Money\n";
        cout << "[5] Close Account\n";
        cout << "[6] Change Password\n";
        cout << "[7] Logout\n";
        while (true)
        {
            cout << "\nEnter your choice: ";
            cin >> choice;
            // التحقق من الإدخال الصحيح
            if (cin.fail() || choice < 0)
            {
                cout << "\nInvalid amount! Please enter a number.\n";
                cin.clear(); // تصحيح خطأ الإدخال
                cin.ignore(10000, '\n'); // حذف الإدخال الخاطئ
                continue;
            }
            break; // الخروج من الحلقة عند الإدخال الصحيح
        }

        cin.ignore();

        // تنفيذ الخيار المحدد
        switch (choice) {
        case 1:
                // الإيداع
                double depositAmount;
                while (true)
                {
                    cout << "\nEnter deposit amount: ";
                    cin >> depositAmount;
                    // التحقق من الإدخال الصحيح
                    if (cin.fail() || depositAmount < 0)
                    {
                        cout << "\nInvalid amount! Please enter a valid positive number.\n";
                        cin.clear(); // تصحيح خطأ الإدخال
                        cin.ignore(10000, '\n'); // حذف الإدخال الخاطئ
                        continue;
                    }

                    break; // الخروج من الحلقة عند الإدخال الصحيح
                }
                balances[userIndex] += depositAmount;
                cout << "\nDeposit successful!\n";
                break;
      case 2:
                // السحب
                double withdrawAmount;
                while (true)
                {
                    cout << "\nEnter withdrawal amount: ";
                    cin >> withdrawAmount;
                    // التحقق من الإدخال الصحيح
                    if (cin.fail() || withdrawAmount < 0)
                    {
                        cout << "\nInvalid amount! Please enter a valid positive number.\n";
                        cin.clear(); // تصحيح خطأ الإدخال
                        cin.ignore(10000, '\n'); // حذف الإدخال الخاطئ
                        continue;
                    }
                    break; // الخروج من الحلقة عند الإدخال الصحيح
                }
                if (withdrawAmount > balances[userIndex])
                {
                    cout << "\nInsufficient balance!\n";
                }

                else
                {
                    balances[userIndex] -= withdrawAmount;
                    cout << "\nWithdrawal successful!\n";
                }
                break;
      case 3:
                // التحقق من الرصيد
                cout << "\nYour current balance: " << balances[userIndex] << "\n";
                break;
      case 4:
                // التحويل
                transferMoney(userIndex);
                break;
      case 5:
                // إغلاق الحساب
                closeAccount(userIndex);
                return;
      case 6:
                // تغيير كلمة المرور
                changePassword(userIndex);
                break;
      case 7:
                // تسجيل الخروج
                cout << "\nLogging out...\n";
                return;
      default:
                cout << "\nInvalid choice! Try again.\n";
    }
  } while (true);
}

// دالة للتحويل
void transferMoney(int userIndex)
{
    string recipient;
    double amount;

    // طلب اسم المستلم
    cout << "\nEnter recipient name: ";
    cin.ignore();
    getline(cin, recipient);

    // تحديد فهرس المستلم
    int recipientIndex = findUserIndex(recipient);

    if (recipientIndex == -1)
    {
        cout << "\nRecipient account not found!\n";
        return;
    }

    // طلب مبلغ التحويل
    while (true)
    {
        cout << "\nEnter amount to transfer: ";
        cin >> amount;
        // التحقق من الإدخال الصحيح
        if (cin.fail() || amount < 0)
        {
            cout << "\nInvalid amount! Please enter a valid positive number.\n";
            cin.clear(); // تصحيح خطأ الإدخال
            cin.ignore(10000, '\n'); // حذف الإدخال الخاطئ
            continue;
        }

        break; // الخروج من الحلقة عند الإدخال الصحيح
    }
    // التحقق من كفاية الرصيد
    if (amount > balances[userIndex])
    {
        cout << "\nInsufficient balance!\n";
        return;
    }

    // تحويل المبلغ
    balances[userIndex] -= amount;
    balances[recipientIndex] += amount;

    cout << "\nTransfer successful!\n";
}

// دالة لتغيير كلمة المرور
void changePassword(int userIndex)
{
    string oldPassword, newPassword, confirmPassword;

    // طلب كلمة المرور الحالية
    cout << "\nEnter your current password: ";
    cin >> oldPassword;

    // التحقق من كلمة المرور الحالية
    if (oldPassword != passwords[userIndex])
    {
        cout << "\nIncorrect password. Password change failed.\n";
        return;
    }

    // طلب كلمة المرور الجديدة وتحقق من تطابقها مع التأكيد
    do
    {
        cout << "\nEnter a new 4-digit password: ";
        cin >> newPassword;

        if (newPassword.length() != 4 || !isNumericPassword(newPassword))
        {
            cout << "\nPassword must be exactly 4 digits and contain numbers only.\n";
            continue;
        }

        cout << "\nConfirm your new password: ";
        cin >> confirmPassword;
        if (newPassword != confirmPassword)
        {
            cout << "\nPasswords do not match. Try again.\n";
        }

  } while (newPassword != confirmPassword);

    // تحديث كلمة المرور
    passwords[userIndex] = newPassword;

    cout << "\nPassword changed successfully!\n";
}

// دالة لإغلاق الحساب
void closeAccount(int userIndex)
{
    // التحقق من أن الرصيد صفرًا
    if (balances[userIndex] > 0)
    {
        cout << "\nYou must withdraw all your money before closing the account.\n";
        return;
    }

    // تحريك البيانات من الحسابات التالية إلى الحساب الحالي
    for (int i = userIndex; i < userCount - 1; i++)
    {
        usernames[i] = usernames[i + 1];
        passwords[i] = passwords[i + 1];
        balances[i] = balances[i + 1];
        loginAttempts[i] = loginAttempts[i + 1];
        accountLocked[i] = accountLocked[i + 1];
    }

    // تناقص عدد الحسابات
    userCount--;

    cout << "\nAccount closed successfully!\n";
}

    // البرنامج الرئيسي
int main()
{
    // عرض شعار البنك
    displayLogo();

    int choice;
    do
    {
        // عرض القائمة الرئيسية وتحديد الخيار
        showMenu();
        if (!(cin >> choice))
        {
            cout << "\nInvalid choice! Please try again.\n";
            cin.clear();
            cin.ignore(10000, '\n');
            continue;
        }
    cin.ignore();

    // تنفيذ الخيار المحدد
    switch (choice)
    {
      case 1:
            createAccount();
            break;
      case 2:
            login();
            break;
      case 3:
            cout << "\nExiting the system. Thank you!\n";
            return 0;
      default:
            cout << "\nInvalid choice! Please try again.\n";
    }
  } while (true);
  return 0;
}

