.data 

inital_balance: .asciiz "Please enter your starting balance: "
init_error: .asciiz "INVALID!\n\n"

menu_str: .asciiz "\nMENU\n1. Withdraw\n2. Deposit\n3. Check Balance\n4. Exit\n"
user_menu_choice: .asciiz ":>"
option_error: .asciiz "\nINVALID!\n"

withdrawal_str: .asciiz "\nHow much would you like to withdraw?\n:>"
withdraw_error: .asciiz "Insufficient funds! Please enter a valid amount.\n"
deposit_str: .asciiz "\nHow much would you like to deposit?\n:>"
new_balance_str: .asciiz "\nYour new balance is: $"
balance_str: .asciiz "\nYour current balance is: $"
exit_prompt: .asciiz "Thank you!\n"

.text 
.globl main 

main:
	li $v0,4
	la $a0, inital_balance
	syscall

	li $v0, 5
	syscall 
	move $t0, $v0
	
	blez $t0, mainError
	j menu
	
	
mainError:
	li $v0,4
	la $a0, init_error
	syscall
	
	j main
	
	
menu:
	li $v0, 4
	la $a0, menu_str
	syscall 
	la $a0, user_menu_choice
	syscall 
	
	li $v0, 5 
	syscall 
	move $t5, $v0
	
	addi $t1, $zero, 1
	addi $t2, $zero, 2
	addi $t3, $zero, 3
	addi $t4, $zero, 4
	
	beq $t5, $t1, withdraw
	beq $t5, $t2, deposit
	beq $t5, $t3, balanceProbe
	beq $t5, $t4, end

	li $v0, 4
	la $a0, option_error
	syscall

	j menu


withdraw:
	li $v0, 4
	la $a0, withdrawal_str
	syscall  

	li $v0, 5 
	syscall 
	move $t5, $v0
	
	sub $t6, $t0, $t5
	addi $t6, $t6, 1
	blez $t6, withdrawError
 
	sub $t0, $t0, $t5
	jal newBalance

	j menu
	
	
withdrawError:
	li $v0,4
	la $a0, withdraw_error
	syscall
	
	j withdraw
	
deposit:
	li $v0, 4
	la $a0, deposit_str
	syscall  
	
	li $v0, 5 
	syscall 
	move $t5, $v0
	
	add $t0, $t0, $t5 
	jal newBalance

	j menu
	

newBalance:
	li $v0, 4
	la $a0, new_balance_str
	syscall
	li $v0, 1
	add $a0, $zero, $t0
	syscall
	
	jr $ra
	
	
balanceProbe:
	li $v0, 4
	la $a0, balance_str
	syscall
	li $v0, 1
	add $a0, $zero, $t0
	syscall 

	j menu
	

end:
	li $v0,4
	la $a0, exit_prompt 
	syscall

	li $v0, 10
	syscall  
