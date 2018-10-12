+++
date = "2017-10-16T10:00:00.0000000+00:00"
tags = ["Python"]
title = "Sending an email with Python"
+++
I haven't done any Python programming in years. I think it's probably 10 years
ago, and then it was only some basic learning-how-to-program stuff.

I'm participating in a project at work. We're doing some prototyping with a
Raspberry Pi and some custom software. While the software works brilliantly, we
want to bolt some extra features onto it.

Enter Python!
The first thing I thought I'd tackle is sending an email. I figured this would
be the most complex piece of code I'd have to write. If I could get that done,
the rest would be a piece of cake. Or so I thought...

My first instinct was of course to end up on Stack Overflow. My google-fu landed
me on this [https://stackoverflow.com/questions/16968758]  question and answer.



Turns out it's really simple. NICE!

Sending an email
By importing smtplib  from the standard library, you're able to send emails
quite easily. I had to look the syntax of function definitions and I also took
the time look up the style guide for Python. I always prefer using the best
practice naming standard of a language.

This led me to write the following function:

def send_mail(
        sender,
        receivers: List[str],
        subject,
        body,
        username,
        password,
        server_address
):
    """Sends an email to the specified receivers using an Exchange server."""
    message = MIMEMultipart()
    message["From"] = sender
    message["To"] = ",".join(receivers)
    message["Subject"] = subject
    message.attach(MIMEText(body))

    server = smtplib.SMTP(server_address, 587)
    server.starttls()
    server.login(username, password)
    server.sendmail(sender, receivers, message.as_string())
    server.quit()


Since I'm using an Exchange server, I have to initiate TLS. Apart from that it's
pretty straight forward.

Running the script in the terminal
I also wanted to be able to use the script by running it int the terminal. This
can be done by calling the function after it has been defined. When the Python
interpreter goes through the file, it'll execute the call to the function.

In Python however it's considered good practice to put the call inside an if
__name__ == "__main__":  clause. This is done to detect if the file is run
directly or imported by another file or module.

When the Python interpreter runs the file directly (for example in the
terminal), it will set the global __name__  variable to __main__. When the file
is imported by another file or module, __name__  will be set to the name of the
file or module.

By putting the execution code inside if __name__ == "__main__":, we're making
sure the code is only run when the file is used directly, and not when it's
imported for use in another peace of code.

Parsing inputs
I wanted to be able to specify the parameters to the send_mail  function by
passing them in when executing the file in the terminal. Turns out Python has a
library for that too!

I love this language!

By importing argparse, I'm able to define parameters, add type hints and help
texts. argparse  also automatically adds an -h  switch which shows all the
possible parameters and their help texts.

Just to make things more interesting, I also provided an option to provide a
file path instead of the body text. The script will then get the text from the
file and use that as the body of the mail.

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Sends an email to the specified receivers using the Sykehuspartner Exchange server.")
    parser.add_argument("sender", type=str, help="Sender's email address.")
    parser.add_argument("receivers", type=str, help="Comma-seperated list of email addresses.")
    parser.add_argument("subject", type=str, help="Subject of the email.")
    parser.add_argument("body", type=str, help="Body text of the email. Can be plain text or a file path when -f is passed.")
    parser.add_argument("username", type=str, help="The username to authenticate with on mail server.")
    parser.add_argument("password", type=str, help="The password to authenticate with on mail server.")
    parser.add_argument("server", type=str, help="The address of the mail server.")
    parser.add_argument("-f", "--file", action="store_true", help="Specifies that the body argument contains a file path instead of plain text")
    args = parser.parse_args()

    if args.file:
        with open(args.body) as file:
            args.body = file.read()

    send_mail(args.sender, args.receivers.split(","), args.subject, args.body, args.username, args.password, args.server)


There isn't much error handling in these scripts, and that's mostly because of
two reasons: It's only going to be used by a small number of internal people and
the error messages from the interpreter will have to do.


--------------------------------------------------------------------------------

After not having used Python for nigh on 10 years, I'm starting to realize what
I've been missing out on. While this is a tiny example, it's been incredibly low
friction. I'm starting to see why so many like Python, and why it's chosen as a
starter language in some universities.

I'm looking forward to playing around with Python some more.

Happy coding!
