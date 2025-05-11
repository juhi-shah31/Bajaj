import requests
import json

def generate_webhook_and_token(name, registration_number, email):
    """
    Sends a POST request to obtain the webhook URL and access token.
    """
    url = "https://bfhldevapigu.healthrx.co.in/hiring/generatewebhook/PYTHON"
    body = {
        "name": name,
        "registration_number": registration_number,
        "email": email
    }
    response = requests.post(url, json=body)
    if response.status_code == 200:
        data = response.json()
        return data.get("webhookUrl"), data.get("accessToken")
    else:
        raise Exception(f"Failed to get webhook and token. Status code: {response.status_code}")

def get_sql_query_for_question_1():
    """
    Returns the SQL query for Question 1 based on the problem statement.
    """
    sql_query = """
    SELECT P.AMOUNT_CREDITED AS SALARY,
           CONCAT(E.FIRST_NAME, ' ', E.LAST_NAME) AS NAME,
           TIMESTAMPDIFF(YEAR, E.DOB, CURDATE()) AS AGE,
           D.DEPT_NAME AS DEPARTMENT_NAME
    FROM PAYMENTS P
    JOIN EMPLOYEE E ON P.EMP_ID = E.EMP_ID
    JOIN DEPARTMENT D ON E.DEPT_ID = D.DEPT_ID
    WHERE P.AMOUNT_CREDITED = (SELECT MAX(AMOUNT_CREDITED) FROM PAYMENTS WHERE DAY(TRANSACTION_DATE) != 1)
      AND DAY(P.TRANSACTION_DATE) != 1
    """
    return sql_query

def submit_solution(access_token, sql_query):
    """
    Sends a POST request to submit the SQL query solution.
    """
    url = "https://bfhldevapigu.healthrx.co.in/hiring/testwebhook/PYTHON"
    headers = {
        "Authorization": f"Bearer {access_token}"
    }
    body = {
        "finalQuery": sql_query
    }
    response = requests.post(url, json=body, headers=headers)
    if response.status_code == 200:
        return True
    else:
        raise Exception(f"Failed to submit solution. Status code: {response.status_code}")

def main(name, registration_number, email):
    
    webhook_url, access_token = generate_webhook_and_token(name, registration_number, email)
    
  
    sql_query = get_sql_query_for_question_1()
  
    success = submit_solution(access_token, sql_query)
    if success:
        print("Solution submitted successfully")
    else:
        print("Failed to submit solution")

# Example usage
if __name__ == "__main__":
    name = "John Doe"
    registration_number = "REG12347"
    email = "john@example.com"
    main(name, registration_number, email)
