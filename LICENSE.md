package com.fdmgroup;

import org.apache.log4j.*;
import com.fdmgroup.Calc;


/**@return This class constructs a BODMAS calculator taking String input and returning double values
*@author Cihan Altunok
*@version 1
*/


class Calc 
{
private static Logger log= Logger.getLogger(Calc.class);
public static void main( String[] args ) 
	
	{	
	new Calc();
	}
	
	public Calc() 
	{
	PropertyConfigurator.configure("src/main/resources/log4j.properties");
	}
	
/**@param Exprs
@return expression as Double */
	public double evaluate(String exprs) 
	{
	return Double.parseDouble(exprs);
	}
		
	/**@param operators
 @return the priority of the operators for BODMAS
 @see the BODMAS precedence can easily be changed by 
modifying the return values for each case */

	public int setPriority (char operators) 
	{
		switch (operators)
		{
		case '(' :return 3; 
		case ')' :return 0; 
		case '*': return 2; 
		case '/':return 2; 
		case '+':return 1; 
		case '-':return 1; 
		default: return 0;
		}

	}

	/**@param exprs.
@return the expression if no operators used.
@see the second if statements deals with a negative value such as (-2)as and operand.
*/
	public double displayExpression ( String exprs)
	{
		int operatCount = 0;   
		char[] numbers = exprs.toCharArray(); 
		exprs=exprs.replaceAll(" ","");

		if (operatCount==0)                         
			return Double.parseDouble(exprs);

		else if (operatCount==1 && numbers[0]=='-') 
			return Double.parseDouble(exprs);
		return displayExpression(exprs);
	}

	/**@param exprs
 @return counting the operands.Ignoring the spaces
*/
public double count(String exprs)

	{
		exprs=exprs.replaceAll(" ","");
		int operatCount = 0;   
		char[] numbers = exprs.toCharArray();   
		Double.parseDouble(exprs);

		for(int i=0;i<numbers.length;i++)  
		{	
			if (numbers[i]>0)
				++operatCount;
		}

		return operatCount;
	}


/**@param exprs.
@return the correct addition when many positive or negative operands used.
@throws exception if the operator contains String values.
@throws exception if the expression ends with a plus operator.
*/
	public double recursiveAddition (String exprs)
	{ 
		char[] numbers = exprs.toCharArray();                    			
		int upperPriority = 0;                              
		int operatCount = 0;                               

		for(int i=0;i<numbers.length;i++)  
		{
			if(setPriority(numbers[i])>upperPriority)
				upperPriority=setPriority(numbers[i]); 

			if (setPriority(numbers[i])>0)
				++operatCount;
		}

		if (operatCount==0)                         
			return Double.parseDouble(exprs);

		if (upperPriority==1)                   
		{
			if (operatCount==1 && numbers[0]=='-') 
				return Double.parseDouble(exprs);

			int operatPosition = -1;               
			boolean ifPlusOperator = true;
			for(int i=0;i<numbers.length;i++) 
			{ 	
				if (numbers[i]=='+' && i !=0 ) 
				{
					operatPosition = i;
					ifPlusOperator=numbers[i]=='+'?true:false; 

					if (numbers[i]!='+')
					{
						throw new IllegalArgumentException("Please use + to add");
					}

					break;
				}
			}			
			int leftOperandStart = 0;
			int rightOperandEnd=operatPosition+1; 
			if (rightOperandEnd=='+' )
			{
				throw new ArrayIndexOutOfBoundsException("Sync error, please try again");
			}

			if (numbers[rightOperandEnd]=='-') 
			{
				rightOperandEnd++;	
			}
			while (rightOperandEnd<numbers.length && setPriority(numbers[rightOperandEnd])<1)
			{
				rightOperandEnd++;             				
			}  

			double leftOperand=Double.parseDouble(exprs.substring(0,operatPosition));
			double rightOperand=Double.parseDouble(exprs.substring(operatPosition+1,rightOperandEnd)); 
			double innerResult = 0;

			if (ifPlusOperator) innerResult= leftOperand + rightOperand;
			return recursiveAddition(""+innerResult+exprs.substring(rightOperandEnd));
		}
		return recursiveAddition(exprs);
	}

/**@param exprs.
@return the correct subtraction when many positive or negative operands used.
@throws exception if the operator contains String values.
@throws exception if the expression ends with a minus operator.
*/
	public double recursiveSubtraction (String exprs)
	{
		char[] numbers = exprs.toCharArray();                    			
		int upperPriority = 0;                              
		int operatCount = 0;                               

		for(int i=0;i<numbers.length;i++)  
		{
			if(setPriority(numbers[i])>upperPriority)
				upperPriority=setPriority(numbers[i]); 

			if (setPriority(numbers[i])>0)
				++operatCount;
		}

		if (operatCount==0)                         
			return Double.parseDouble(exprs);

		if (upperPriority==1)                   
		{
			if (operatCount==1 && numbers[0]=='-') 
				return Double.parseDouble(exprs);

			int operatPosition = -1;               
			boolean ifMinusOperator = true;
			for(int i=0;i<numbers.length;i++) 
			{ 	
				if (numbers[i]=='-' && i !=0 ) 
				{
					operatPosition = i;
					ifMinusOperator=numbers[i]=='-'?true:false; 

					if (numbers[i]!='-')
					{
						throw new IllegalArgumentException("Please use - to subtract");
					}

					break;
				}
			}			
			int leftOperandStart = 0;
			int rightOperandEnd=operatPosition+1; 

			if (rightOperandEnd=='-' )
			{
				throw new ArrayIndexOutOfBoundsException("Sync error, please try again");
			}
			if (numbers[rightOperandEnd]=='-') 
			{
				rightOperandEnd++;	
			}
			while (rightOperandEnd<numbers.length && setPriority(numbers[rightOperandEnd])<1)
			{
				rightOperandEnd++;             				
			}  

			double leftOperand=Double.parseDouble(exprs.substring(0,operatPosition));
			double rightOperand=Double.parseDouble(exprs.substring(operatPosition+1,rightOperandEnd)); 
			double innerResult = 0;

			if (ifMinusOperator) innerResult= leftOperand - rightOperand;
			return recursiveSubtraction(""+innerResult+exprs.substring(rightOperandEnd));
		}
		return recursiveSubtraction(exprs);
	}	

/**@param exprs.
@return the correct multiplication when many positive or negative operands used.
@throws exception if the operator contains String values.
@throws exception if the expression ends with a multiplication operator.
*/	
	public double recursiveMultiplication (String exprs)
	{
	char[] numbers = exprs.toCharArray();                    			
	int upperPriority = 0;                              
	int operatCount = 0;                               
		
		for(int i=0;i<numbers.length;i++)  
		{
		if(setPriority(numbers[i])>upperPriority)
		upperPriority=setPriority(numbers[i]); 
		
		if (setPriority(numbers[i])>0)
		++operatCount;
		}
		if (operatCount==0)                         
		return Double.parseDouble(exprs);
	
		boolean ifMultipleOperator=true;          
		if (upperPriority==2 && ifMultipleOperator==true )                   
		{
			
		int operatPosition = -1;               
	
			for(int i=0;i<numbers.length;i++) 
			{
				if (numbers[i]=='*' && i !=0) 
				{
				operatPosition = i;
				ifMultipleOperator=numbers[i]=='*'?true:false;  
					
			
			if (numbers[i]!='*')
					{
					throw new IllegalArgumentException("Please use * to multiply");
					}
			
				break;
				}
			}
		  
		int leftOperandStr=operatPosition-1; 
		
			while (leftOperandStr>=0 && setPriority(numbers[leftOperandStr])<=1)
			{
			leftOperandStr--;	
			}

		int rightOperandEnd=operatPosition+1; 
			if (rightOperandEnd=='*' )
			{
			throw new ArrayIndexOutOfBoundsException("Sync error, please try again");
			}
			if (numbers[rightOperandEnd]=='-') 
			{
			rightOperandEnd++;	
			}
				while (rightOperandEnd<numbers.length && setPriority(numbers[rightOperandEnd])<1)
				{
				rightOperandEnd++;            
				}  
		
			double leftOperand=Double.parseDouble(exprs.substring(leftOperandStr+1, operatPosition));
			double rightOperand=Double.parseDouble(exprs.substring(operatPosition+1,rightOperandEnd ));
			double innerResult = 0;
		
			if (ifMultipleOperator) innerResult= leftOperand * rightOperand;
			return recursiveMultiplication(exprs.substring(0,leftOperandStr+1) + innerResult+exprs.substring(rightOperandEnd));
	
		}
	return recursiveMultiplication(exprs);
	}
/**@param exprs.
@return the correct division when many positive or negative operands used.
throws exception if the divisor is 0
@throws exception if the operator contains String values.
@throws exception if the expression ends with a division operator.
*/	
	public double recursiveDivision (String exprs)
	{
	char[] numbers = exprs.toCharArray();                    			
	int upperPriority = 0;                              
	int operatCount = 0;                               
		
		for(int i=0;i<numbers.length;i++)  
		{
		if(setPriority(numbers[i])>upperPriority)
		upperPriority=setPriority(numbers[i]); 
		
		if (setPriority(numbers[i])>0)
		++operatCount;
		}
		if (operatCount==0)                         
		return Double.parseDouble(exprs);
	
		boolean ifDivideOperator=true;          
		if (upperPriority==2 && ifDivideOperator==true )                   
		{
		
		int operatPosition = -1;               
	
			for(int i=0;i<numbers.length;i++) 
			{
				if (numbers[i]=='/' && i !=0) 
				{
				operatPosition = i;
				ifDivideOperator=numbers[i]=='/'?true:false;  
				
				if (numbers[i]!='/')
					{
					throw new IllegalArgumentException("Please use / to divide");
					}
				
				break;
				}
			}
		  
		 int leftOperandStr=operatPosition-1; 
		
			while (leftOperandStr>=0 && setPriority(numbers[leftOperandStr])<=1)
			{
			leftOperandStr--;	
			}

		int rightOperandEnd=operatPosition+1; 
			if (rightOperandEnd=='/' )
			{
			throw new ArrayIndexOutOfBoundsException("Sync error, please try again");
			}
			if (numbers[rightOperandEnd]=='-') 
			{
			rightOperandEnd++;	
			}
				while (rightOperandEnd<numbers.length && setPriority(numbers[rightOperandEnd])<1)
				{
				rightOperandEnd++;            
				}  
		
			double leftOperand=Double.parseDouble(exprs.substring(leftOperandStr+1, operatPosition));
			double rightOperand=Double.parseDouble(exprs.substring(operatPosition+1,rightOperandEnd ));
			
			if (rightOperand==0)
			{
			throw new ArithmeticException("Ma Error"+"Cannot divide by zero.");
			}
			double innerResult = 0;
		
			if (ifDivideOperator) innerResult= leftOperand / rightOperand;
			return recursiveDivision(exprs.substring(0,leftOperandStr+1) + innerResult+exprs.substring(rightOperandEnd));
	
		}
	return recursiveDivision(exprs);
	}

	/**@param exprs.
@return the four operations recursively in parenthesis.
@throws exceptions if the open parenthesis is not closed.
*/
	public double recursiveAdditionInParanthesis(String exprs)
	{
	char[] numbers = exprs.toCharArray();                    			
	int prtsOpen= -1;
	int prtsClosed=-1;
	int prtsOpenEndDiff=0;
	
		for(int i=0;i<numbers.length;i++) 
		{
			if (numbers[i]=='(')
			{
			prtsOpen=prtsOpen<0?i:prtsOpen;  
			prtsOpenEndDiff++;
			}
			else if (numbers[i]==')')
			{
			prtsOpenEndDiff--;												
				
				if (prtsOpenEndDiff==0) 
					{
					prtsClosed=i;
					break; 
					}
			}
		}
	double innerResult=recursiveAddition(exprs.substring(prtsOpen+1,prtsClosed));
	return recursiveAddition(exprs.substring(0,prtsOpen) + innerResult + exprs.substring(prtsClosed+1));
	}
	/**@param exprs.
@return the subtraction of operands recursively in parenthesis.
@throws exceptions if the open parenthesis is not closed.
*/

	public double recursiveSubtractionInParanthesis(String exprs)
	{
	char[] numbers = exprs.toCharArray();                    			
	int prtsOpen= -1;
	int prtsClosed=-1;
	int prtsOpenEndDiff=0;
	
		for(int i=0;i<numbers.length;i++) 
		{
			if (numbers[i]=='(')
			{
			prtsOpen=prtsOpen<0?i:prtsOpen;  
			prtsOpenEndDiff++;
			}
			else if (numbers[i]==')')
			{
			prtsOpenEndDiff--;												
				
				if (prtsOpenEndDiff==0) 
					{
					prtsClosed=i;
					break; 
					}
			}
		}
	double innerResult=recursiveSubtraction(exprs.substring(prtsOpen+1,prtsClosed));
	return recursiveSubtraction(exprs.substring(0,prtsOpen) + innerResult + exprs.substring(prtsClosed+1));
}
	/**@param exprs.
@return the multiplication of operands recursively in parenthesis.
@throws exceptions if the open parenthesis is not closed.
*/
	public double recursiveMultiplicationInParanthesis(String exprs)
	{
	char[] numbers = exprs.toCharArray();                    			
	int prtsOpen= -1;
	int prtsClosed=-1;
	int prtsOpenEndDiff=0;
	
		for(int i=0;i<numbers.length;i++) 
		{
			if (numbers[i]=='(')
			{
			prtsOpen=prtsOpen<0?i:prtsOpen;  
			prtsOpenEndDiff++;
			}
			else if (numbers[i]==')')
			{
			prtsOpenEndDiff--;												
				
				if (prtsOpenEndDiff==0) 
					{
					prtsClosed=i;
					break; 
					}
			}
		}
	double innerResult=recursiveMultiplication(exprs.substring(prtsOpen+1,prtsClosed));
	return recursiveMultiplication(exprs.substring(0,prtsOpen) + innerResult + exprs.substring(prtsClosed+1));
}
/**@param exprs.
@return the division of operands recursively in parenthesis.
@throws exceptions if the open parenthesis is not closed.
*/	

	public double recursiveDivisionInParanthesis(String exprs)
	{
	char[] numbers = exprs.toCharArray();                    			
	int prtsOpen= -1;
	int prtsClosed=-1;
	int prtsOpenEndDiff=0;

		for(int i=0;i<numbers.length;i++) 
		{
		
			if (numbers[i]=='(')
			{
			prtsOpen=prtsOpen<0?i:prtsOpen;  
			prtsOpenEndDiff++;
			}
			else if (numbers[i]==')')
			{
			prtsOpenEndDiff--;												
		
				if (prtsOpenEndDiff==0) 
				{
				prtsClosed=i;
				break; 
				}
			}

		}
	double innerResult=recursiveDivision(exprs.substring(prtsOpen+1,prtsClosed));
	return recursiveDivision(exprs.substring(0,prtsOpen) + innerResult + exprs.substring(prtsClosed+1));
	}
}

	/**working progress
	public double evaluateBODMAS (String exprs)
{
	{	
	}

}	
return evaluateBODMAS(exprs);
}
}

*/
