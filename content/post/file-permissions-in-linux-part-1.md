+++
date = "03/30/2018 10:47:00 +00:00"
tags = ["Linux","Tip"]
title = "File permissions in Linux - Part 1"
+++
When working with Linux, you'll come across file permissions at a pretty early
stage. As a persons who's born and bred on Windows, that was a strange new
territory to me. Here's an introduction to permissions.

To see the permissions of a file, you can use the ls -l  command on a file, like
so:

ls -l myfile.txt
# outputs
-rwxr-xr--  1 jon  staff  5 Feb 21 22:06 myfile.txt


This gives us some information about the file. We'll go through each of the bits
of information, but we'll do it backwards. That way we can start with the simple
stuff.

my file.txt  is simply the name of the file you're looking at.

Feb 21 22:06  is the timestamp of when the file was modified last. In other
words; it get's updated when you make changes to the file.

5  is the size of the file. The size is listed in bytes, so the number can
quickly get large.

staff  is the name of the group the file belongs to. All files belong to a
group. Users who are in the same group can have special permissions in regards
to the file. This makes it possible to give a group of users the ability to
modify the file, whiie all others can only view the file.

jon  is the owner of the file. The person owning the file usually was the one to
create the file. The owner is also usually the main user of the file.

1  is

Before we cover the last piece of information, we'll get some terminology in
place. This makes sure we all know what we're talking about:

 * read  (r): Read permission gives a user the ability to see the contents of a
   file. They are not able to change the file.
 * write  (w): Write permission gives a user the ability to make changes to a
   file. Write permission to a file doesn't automatically give a user read 
   permission. This means it's possible for a user to make changes to a file,
   without seeing what's inside the file.
 * execute  (x): Execute permission gives a user the ability to run (or execute
   a file). This is particularly in relation to script files or executables.
   They can not be run unless the user has execute permission.

The permissions are fully independent. If a user has one of the permissions, the
user does not receive one of the others automatically. A user can for example be
able to run a file (execute), without being able to see the contents (read) or
change the file (write).

Now let's look at the last piece of information.

-rwxr-xr-- is a summary of all the permissions that are active for the file. The
permissions are grouped in the following pattern 1-3-3-3. Let's look at those
groups in order.

-: The first group indicates what kind of item you're looking at. A dash (-)
indicates a file. Directories (or folders) are marked with a d.

rwx: The next group indicates what permissions the owner of the file has. The
first position is an r  if the owner has read permission to the file. If the
owner does not have read permission a dash (-) will be shown instead. The next
position is a w  if the owner has write permission, or a dash (-) if not. The
last position is an x  if the owner has execute permission, or a dash (-) if
not. The active permissions for the owner is read, write  and execute.

r-x: This group follows the same rules as the previous one, but they apply to
the group the file belongs to. Any user that's a member of the group will have
these permissions. As the file's group is staff, all users in staff  have read 
and execute  permissions.

r—: This group again follows the same rules as the two previous ones, but the
permissions apply to all other users that are not the owner or members of the
group the file belongs to. Any user connected to the system will have read 
permission.

It's worth mentioning that ls -l  also works on directories. When used on a
directory it will list the permissions of all the items inside the directory. If
you don't specify a file or directory (after the -l) it will list the directory
you're currently in:

ls -l
# outputs
-rw-r--r--  1 jon  staff  0 Feb 21 22:47 index.html
-rwxr-xr--  1 jon  staff  5 Feb 21 22:06 myfile.txt


In my next post, I'll look go through how to change permissions. See you there.
