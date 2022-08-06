import re
import json


def validateEmail(email):
    index_of_at = email.index("@")
    index_of_dot = email.index(".")
    letters_before_at = email[:index_of_at]
    valid_letters = re.search("^[a-zA-Z]", email) and len(letters_before_at) > 0
    if email.count("@") == 1 and email.count(".") == 1 and (index_of_dot - index_of_at) > 1 and index_of_dot > index_of_at and valid_letters:
        return True
    return False


def validatePassword(password):
    password_len = len(password)
    has_req_letters = bool(re.search("(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*#?&])[A-Za-z\d@$!#%*?&]",password)) ==True
    if 5 < password_len > 16 and has_req_letters:
        return True
    return False


def registration(email, password):
    is_valid_email = validateEmail(email)
    is_valid_password = validatePassword(password)
    return is_valid_email and is_valid_password


def choice(arg):
    match arg:
        case 1:
            email = input("Enter email to register\n")
            password = input("Type new password\n")
            registered = False
            while True:
                is_reg_success = registration(email, password)
                if is_reg_success:
                    data = {}
                    with open('loginDetails.txt', 'r+') as f:
                        file_content = f.read()
                        if file_content:
                            data = json.loads(file_content)
                            data[email] = password
                        else:
                            data[email] = password
                        f.seek(0)
                        f.truncate()
                        f.write(json.dumps(data))
                        f.close()
                    print("Registration Successful")
                    registered = True
                    break
                else:
                    email = input("Enter valid email to register\n")
                    password = input("Type valid password\n")
            return registered
        case 2:
            email = input("Enter Username / Email\n")
            with open('loginDetails.txt', 'r+') as f:
                file_content = f.read()
                if file_content:
                    data = json.loads(file_content)
                    if email in data.keys():
                        n = int(input("Enter 1 Password, enter 2 for new password\n"))
                        match n:
                            case 1:
                                print(f"your password {data[email]}")
                                print("Login Successful")
                                f.close()
                                return True
                            case 2:
                                new_pass = input("Enter new password\n")
                                while True:
                                    if validatePassword(new_pass):
                                        data[email] = new_pass
                                        break
                                    else:
                                        new_pass = input("Enter valid new password\n")
                                f.seek(0)
                                f.truncate()
                                f.write(json.dumps(data))
                                f.close()
                                return True
                    else:
                        print("No Details Available Redirecting to registration")
                        f.close()
                        return choice(1)
                else:
                    print("No Details Available Redirecting to registration")
                    f.close()
                    return choice(1)
        case default:
            return False

